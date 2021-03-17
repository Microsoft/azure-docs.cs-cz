---
title: Nastavení e-mailové adresy jako metody ověřování – Azure AD
description: Jak nastavit stránku informací o zabezpečení (Preview) k ověření vaší identity pomocí e-mailové adresy jako metody ověřování.
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
ms.openlocfilehash: 4f0ea55149fc8cfeeb6ef719d67346019b81078a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83744475"
---
# <a name="set-up-an-email-address-as-your-verification-method"></a>Nastavení e-mailové adresy jako metody ověřování

Pomocí těchto kroků můžete přidat metodu resetování hesla. Po prvním nastavení se můžete vrátit na stránku **bezpečnostní údaje** a přidat, aktualizovat nebo odstranit informace o zabezpečení.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Pokud nevidíte možnost e-mailu, je možné, že vaše organizace neumožňuje použít tuto možnost pro metodu resetování hesla. V takovém případě budete muset zvolit jinou metodu, nebo se obraťte na helpdesk vaší organizace, kde najdete další nápovědu.

## <a name="security-vs-password-reset-verification"></a>Ověření zabezpečení a resetování hesla

Metody bezpečnostních údajů se používají pro ověřování dvou faktorů zabezpečení i pro resetování hesla. Nicméně ne všechny metody lze použít pro obojí.

| Metoda | Použití |
| ------ | -------- |
| Ověřovací aplikace | Dvojúrovňové ověřování a ověřování nového hesla. |
| Textové zprávy | Dvojúrovňové ověřování a ověřování nového hesla. |
| Telefonní hovory | Dvojúrovňové ověřování a ověřování nového hesla. |
| Bezpečnostní klíč | Dvojúrovňové ověřování a ověřování nového hesla. |
| E-mailový účet | Jenom ověřování heslem. Budete muset zvolit jinou metodu pro dvojúrovňové ověřování. |
| Bezpečnostní otázky | Jenom ověřování heslem. Budete muset zvolit jinou metodu pro dvojúrovňové ověřování. |

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Nastavení e-mailové adresy na stránce bezpečnostní údaje

V závislosti na nastaveních vaší organizace možná budete moci používat vaši e-mailovou adresu jako jednu z vašich metod bezpečnostních údajů.

>[!Note]
>Doporučujeme použít e-mailovou adresu, která nevyžaduje pro přístup k vašemu síťovému heslu. Pokud nevidíte možnost e-mailu, je možné, že vaše organizace neumožňuje použít k ověření e-mail. Pokud se jedná o tento případ, budete muset zvolit jinou metodu, nebo požádejte správce o další nápovědu.

### <a name="to-set-up-your-email-address"></a>Nastavení e-mailové adresy

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu a pak přejít na https://myaccount.microsoft.com/ stránku.

    ![Stránka můj profil zobrazující odkazy na zvýrazněné informace o zabezpečení](media/security-info/securityinfo-myprofile-page.png)

2. V levém navigačním podokně nebo na odkaz v bloku **informací o** zabezpečení vyberte možnost **informace o zabezpečení** a pak vyberte **Přidat metodu** ze stránky **bezpečnostní údaje** .

    ![Stránka informace o zabezpečení se zvýrazněnou možností přidat metodu](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stránce **Přidat metodu** vyberte v rozevíracím seznamu možnost **e-mail** a pak vyberte **Přidat**.

    ![Pole přidat metodu s vybraným e-mailem](media/security-info/securityinfo-myprofile-addemail.png)

4. Na stránce **e-mail** zadejte svou e-mailovou adresu (například alain@gmail.com ) a pak vyberte **Další**.

    ![Přidat telefonní číslo a vybrat telefonní hovory](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Tato e-mailová adresa nemůže být vaším pracovním nebo školním e-mailem.

5. Zadejte kód odeslaný na zadanou e-mailovou adresu a pak vyberte **Další**.

    ![Přidat telefonní číslo a zvolit textové zprávy](media/security-info/securityinfo-myprofile-emailcode.png)

    Vaše bezpečnostní údaje se aktualizují a k ověření vaší identity při použití resetování hesla můžete použít svou e-mailovou adresu.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Odstraňte svou e-mailovou adresu z metod zabezpečení

Pokud už svou e-mailovou adresu nechcete používat jako metodu bezpečnostního údaje, můžete ji odebrat ze stránky **bezpečnostní údaje** .

>[!Important]
>Pokud e-mailovou adresu odstraníte omylem, neexistuje žádný způsob, jak ji vrátit. Tuto metodu budete muset znovu přidat podle kroků v části [Nastavení e-mailové adresy](#set-up-your-email-address-from-the-security-info-page) v tomto článku.

### <a name="to-delete-your-email-address"></a>Odstranění e-mailové adresy

1. Na stránce **informace o zabezpečení** vyberte odkaz **Odstranit** vedle možnosti **e-mail** .

    ![Odkaz pro odstranění metody Phone z bezpečnostních údajů](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Pokud chcete **e-mailový** účet odstranit, vyberte v potvrzovacím poli možnost **Ano** . E-mailový účet se odstraní z bezpečnostních údajů a zmizí ze stránky **bezpečnostní údaje** .

## <a name="additional-security-info-methods"></a>Další metody zabezpečení

Máte k dispozici další možnosti, jak vaše organizace kontaktuje vaši identitu na základě toho, co se you're snaží. Mezi možnosti patří:

- **Ověřovací aplikace** Stáhněte a použijte ověřovací aplikaci k získání oznámení o schválení nebo náhodně generovaného kódu schválení pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny k nastavení a používání aplikace Microsoft Authenticator najdete v tématu [Nastavení bezpečnostních údajů pro použití ověřovací aplikace](security-info-setup-auth-app.md).

- **Text mobilního zařízení** Zadejte číslo mobilního zařízení a získejte text jako kód, který budete používat pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny, jak ověřit identitu pomocí textové zprávy (SMS), najdete v tématu [Nastavení bezpečnostních údajů pro použití zasílání textových zpráv (SMS)](security-info-setup-text-msg.md).

- **Mobilní zařízení nebo pracovní telefonní hovor.** Zadejte číslo mobilního zařízení a získejte telefonní hovor pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny, jak ověřit identitu pomocí telefonního čísla, najdete v tématu [Nastavení bezpečnostních údajů pro použití telefonních hovorů](security-info-setup-phone-number.md).

- **Bezpečnostní klíč** Zaregistrujte svůj bezpečnostní klíč kompatibilní s Microsoftem a použijte ho spolu s kódem PIN pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny, jak ověřit identitu pomocí bezpečnostního klíče, najdete v tématu [Nastavení bezpečnostních údajů pro použití bezpečnostního klíče](security-info-setup-security-key.md).

- **Bezpečnostní otázky.** Odpovězte na některé bezpečnostní otázky, které vytvořil správce vaší organizace. Tato možnost je k dispozici pouze pro resetování hesla a nikoli pro dvoustupňové ověřování. Podrobné pokyny k nastavení bezpečnostních otázek najdete v článku [Nastavení bezpečnostních informací pro použití bezpečnostních otázek](security-info-setup-questions.md) .

    >[!Note]
    >Pokud některé z těchto možností chybí, je to pravděpodobně proto, že vaše organizace tyto metody nepovoluje. Pokud se jedná o tento případ, budete muset zvolit dostupnou metodu nebo požádat správce o další nápovědu.

## <a name="next-steps"></a>Další kroky

- Resetujte heslo, pokud jste ho ztratili nebo zapomněli z [portálu pro resetování hesla](https://passwordreset.microsoftonline.com/) , nebo postupujte podle pokynů v článku [resetování svého pracovního nebo školního hesla](active-directory-passwords-update-your-own-password.md) .

- Získejte tipy pro řešení potíží a nápovědu k problémům s přihlášením v tématu se [nemůžete přihlásit k vašemu účet Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) .
