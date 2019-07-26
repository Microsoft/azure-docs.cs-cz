---
title: Nastavte bezpečnostní údaje (Preview), abyste mohli používat své bezpečnostní otázky – Azure Active Directory | Microsoft Docs
description: Jak nastavit bezpečnostní údaje pro ověření identity pomocí předem definovaných bezpečnostních otázek.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1c375b64d93662ec50923078549c4f2153fba0a
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382813"
---
# <a name="set-up-security-info-preview-to-use-security-questions"></a>Nastavte bezpečnostní údaje (Preview), abyste mohli používat bezpečnostní otázky.

Pomocí těchto kroků můžete přidat metodu resetování hesla. Po prvním nastavení se můžete vrátit na stránku **bezpečnostní údaje** a přidat, aktualizovat nebo odstranit informace o zabezpečení.

Po nastavení metody resetování hesla musíte také nastavit metodu dvou metod ověřování pomocí [ověřovací aplikace](security-info-setup-auth-app.md), [zasílání zpráv SMS](security-info-setup-text-msg.md)nebo [telefonního hovoru](security-info-setup-phone-number.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

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

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu a https://myprofile.microsoft.com/ pak přejít na stránku.

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

## <a name="next-steps"></a>Další postup

- Resetujte heslo, pokud jste ho ztratili nebo zapomněli z [portálu pro resetování hesla](https://passwordreset.microsoftonline.com/) , nebo postupujte podle pokynů v článku [resetování svého pracovního nebo školního hesla](user-help-reset-password.md) .

- Získejte tipy pro řešení potíží a nápovědu k problémům s přihlášením v tématu se nemůžete [přihlásit k vašemu účet Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) .
