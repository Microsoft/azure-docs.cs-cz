---
title: Nastavení bezpečnostních otázek jako metody ověřování – Azure AD
description: Jak nastavit stránku informací o zabezpečení (Preview) k ověření vaší identity pomocí předem definovaných bezpečnostních otázek jako metody ověřování.
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
ms.openlocfilehash: 0c55f15d12ddc51573f59f56c742a8cac525af0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83744438"
---
# <a name="set-up-security-questions-as-your-verification-method"></a>Nastavení bezpečnostních otázek jako metody ověřování

Pomocí těchto kroků můžete přidat metodu resetování hesla. Po prvním nastavení se můžete vrátit na stránku **bezpečnostní údaje** a přidat, aktualizovat nebo odstranit informace o zabezpečení.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Pokud nevidíte možnost bezpečnostní otázky, je možné, že vaše organizace neumožňuje použít tuto možnost pro metodu resetování hesla. V takovém případě budete muset zvolit jinou metodu, nebo se obraťte na helpdesk vaší organizace, kde najdete další nápovědu.

## <a name="security-verification-versus-password-reset-authentication"></a>Ověření zabezpečení oproti ověřování hesla pro resetování hesla

Metody bezpečnostních údajů se používají pro ověřování dvou faktorů zabezpečení i pro resetování hesla. Nicméně ne všechny metody lze použít pro obojí.

| Metoda | Použití |
| ------ | -------- |
| Ověřovací aplikace | Dvojúrovňové ověřování a ověřování nového hesla. |
| Textové zprávy | Dvojúrovňové ověřování a ověřování nového hesla. |
| Telefonní hovory | Dvojúrovňové ověřování a ověřování nového hesla. |
| Bezpečnostní klíč | Dvojúrovňové ověřování a ověřování nového hesla. |
| E-mailový účet | Jenom ověřování heslem. Budete muset zvolit jinou metodu pro dvojúrovňové ověřování. |
| Bezpečnostní otázky | Jenom ověřování heslem. Budete muset zvolit jinou metodu pro dvojúrovňové ověřování. |

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Nastavení bezpečnostních otázek ze stránky bezpečnostní údaje

V závislosti na nastaveních vaší organizace můžete jako jednu z vašich metod zabezpečení zvolit několik bezpečnostních otázek a odpovědět na ně. Správce nastaví počet bezpečnostních otázek, které je nutné vybrat, a odpověď.

Pokud používáte bezpečnostní otázky, doporučujeme je používat ve spojení s jinou metodou. Bezpečnostní otázky můžou být méně bezpečné než jiné metody, protože někteří lidé můžou znát odpovědi na vaše otázky.

> [!Note]
> Bezpečnostní otázky se ukládají soukromě a bezpečně na objekt uživatele v adresáři a můžou na vás odpovědět jenom během registrace. Neexistuje žádný způsob, jak váš správce číst ani upravovat vaše dotazy ani odpovědi.
>
> Pokud nevidíte možnost bezpečnostní otázky, je možné, že vaše organizace neumožňuje používat bezpečnostní otázky k ověření. Pokud se jedná o tento případ, budete muset zvolit jinou metodu, nebo požádejte správce o další nápovědu.
>
> Účty správců neumožňují používat bezpečnostní otázky jako metodu resetování hesla. Pokud jste přihlášeni jako účet úrovně správce, tyto možnosti se nezobrazí.

### <a name="to-set-up-your-security-questions"></a>Nastavení bezpečnostních otázek

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu a pak přejít na https://myaccount.microsoft.com/ stránku.

    ![Stránka můj profil zobrazující odkazy na zvýrazněné informace o zabezpečení](media/security-info/securityinfo-myprofile-page.png)

2. V levém navigačním podokně nebo na odkaz v bloku **informací o** zabezpečení vyberte možnost **informace o zabezpečení** a pak vyberte **Přidat metodu** ze stránky **bezpečnostní údaje** .

    ![Stránka informace o zabezpečení se zvýrazněnou možností přidat metodu](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stránce **Přidat metodu** vyberte v rozevíracím seznamu možnost **bezpečnostní otázky** a pak vyberte **Přidat**.

    ![Přidat pole metody s vybranými bezpečnostními otázkami](media/security-info/securityinfo-myprofile-addquestions.png)

4. Na stránce **bezpečnostní otázky** zvolte a odpovězte na své bezpečnostní otázky a pak vyberte **Uložit**.

    ![Přidat telefonní číslo a vybrat telefonní hovory](media/security-info/securityinfo-myprofile-securityquestions.png)

    Vaše bezpečnostní údaje se aktualizují a k ověření vaší identity při použití resetování hesla můžete použít své bezpečnostní otázky.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Odstranění bezpečnostních otázek z metod zabezpečení

Pokud již nechcete své bezpečnostní otázky používat jako metodu bezpečnostních údajů, můžete je odebrat ze stránky **bezpečnostní údaje** .

>[!Important]
>Pokud bezpečnostní otázky odstraníte omylem, neexistuje žádný způsob, jak to vrátit. Tuto metodu budete muset přidat znovu podle kroků v části [Nastavení bezpečnostních otázek](#set-up-your-security-questions-from-the-security-info-page) v tomto článku.

### <a name="to-delete-your-security-questions"></a>Odstranění bezpečnostních otázek

1. Na stránce **informace o zabezpečení** vyberte odkaz **Odstranit** vedle možnosti **bezpečnostní otázky** .

    ![Odkaz pro odstranění metody Phone z bezpečnostních údajů](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. V potvrzovacím poli vyberte **Ano** , pokud chcete odstranit **bezpečnostní otázky**. Po odstranění bezpečnostních otázek se metoda odebere z bezpečnostních údajů a zmizí ze stránky **bezpečnostní údaje** .

## <a name="additional-security-info-methods"></a>Další metody zabezpečení

Máte k dispozici další možnosti, jak vaše organizace kontaktuje vaši identitu na základě toho, co se you're snaží. Mezi možnosti patří:

- **Ověřovací aplikace** Stáhněte a použijte ověřovací aplikaci k získání oznámení o schválení nebo náhodně generovaného kódu schválení pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny k nastavení a používání aplikace Microsoft Authenticator najdete v tématu [Nastavení bezpečnostních údajů pro použití ověřovací aplikace](security-info-setup-auth-app.md).

- **Text mobilního zařízení** Zadejte číslo mobilního zařízení a získejte text jako kód, který budete používat pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny, jak ověřit identitu pomocí textové zprávy (SMS), najdete v tématu [Nastavení bezpečnostních údajů pro použití zasílání textových zpráv (SMS)](security-info-setup-text-msg.md).

- **Mobilní zařízení nebo pracovní telefonní hovor.** Zadejte číslo mobilního zařízení a získejte telefonní hovor pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny, jak ověřit identitu pomocí telefonního čísla, najdete v tématu [Nastavení bezpečnostních údajů pro použití telefonních hovorů](security-info-setup-phone-number.md).

- **Bezpečnostní klíč** Zaregistrujte svůj bezpečnostní klíč kompatibilní s Microsoftem a použijte ho spolu s kódem PIN pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny, jak ověřit identitu pomocí bezpečnostního klíče, najdete v tématu [Nastavení bezpečnostních údajů pro použití bezpečnostního klíče](security-info-setup-security-key.md).

- **E-mailová adresa** Zadejte svou pracovní nebo školní e-mailovou adresu, abyste získali e-mail pro resetování hesla. Tato možnost není k dispozici pro dvoustupňové ověřování. Podrobné pokyny k nastavení e-mailu najdete v tématu [Nastavení bezpečnostních údajů pro použití e-mailu](security-info-setup-email.md).

    >[!Note]
    >Pokud některé z těchto možností chybí, je to pravděpodobně proto, že vaše organizace tyto metody nepovoluje. Pokud se jedná o tento případ, budete muset zvolit dostupnou metodu nebo požádat správce o další nápovědu.

## <a name="next-steps"></a>Další kroky

- Resetujte heslo, pokud jste ho ztratili nebo zapomněli z [portálu pro resetování hesla](https://passwordreset.microsoftonline.com/) , nebo postupujte podle pokynů v článku [resetování svého pracovního nebo školního hesla](active-directory-passwords-update-your-own-password.md) .

- Získejte tipy pro řešení potíží a nápovědu k problémům s přihlášením v tématu se [nemůžete přihlásit k vašemu účet Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) .
