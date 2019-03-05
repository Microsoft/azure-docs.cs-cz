---
title: Upravit informace o zabezpečení (preview) tak, aby použít bezpečnostní otázky – Azure Active Directory | Dokumentace Microsoftu
description: Jak nastavit své bezpečnostní údaje k ověření vaší identity pomocí předdefinované bezpečnostní otázky.
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
ms.openlocfilehash: 831b17eefb971a5ed617e5cb30730ce23c87e374
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57340453"
---
# <a name="set-up-security-info-preview-to-use-security-questions"></a>Upravit informace o zabezpečení (preview) tak, aby použít bezpečnostní otázky
Můžete následujícím postupem přidáte, že vaše heslo resetováno metoda. Po nastavení tím prvním, můžete se vrátit k **bezpečnostní údaje** stránku chcete přidat, aktualizovat nebo odstranit informace o zabezpečení.

Po nastavení vaše heslo resetováno metodu, musíte také nastavit vaše metoda dvojúrovňového ověřování pomocí [ověřovací aplikaci](security-info-setup-auth-app.md), [zasílání textových zpráv](security-info-setup-text-msg.md), nebo [telefonního hovoru](security-info-setup-phone-number.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Nastavte své bezpečnostní otázky, na stránce informace o zabezpečení
V závislosti na nastavení vaší organizace je možné vybrat a zodpovězení několika otázek zabezpečení jako jeden z vašich metodách informace o zabezpečení. Správce nastaví počet bezpečnostních otázek, je nutné vybrat a odpovědět.

Pokud používáte bezpečnostní otázky, doporučujeme používat je společně s jinou metodu. Bezpečnostní otázky může být méně bezpečné než jiné metody, protože někteří lidé znát odpovědi na své otázky.

>[!Note]
>Bezpečnostní otázky jsou uloženy privátně a bezpečně na objekt uživatele v adresáři a jako odpověď získat pouze poskytnete během registrace. Neexistuje žádný způsob, jak správce, aby číst nebo upravovat otázek a odpovědí.

>Pokud nevidíte možnost otázky zabezpečení, je možné, že vaše organizace nepovoluje budete moci použít bezpečnostní otázky pro ověření. Pokud je to tento případ, budete muset zvolit jinou metodu, nebo požádejte správce o další pomoc.

### <a name="to-set-up-your-security-questions"></a>Nastavení bezpečnostních otázek

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu a potom přejděte k vaší https://myprofile.microsoft.com/ stránky.

    ![Můj profil stránky zobrazující zvýrazněný odkazy informace o zabezpečení](media/security-info/securityinfo-myprofile-page.png)

2. Vyberte **bezpečnostní údaje** v levém navigačním podokně nebo z odkazu v **bezpečnostní údaje** blokovat a pak vyberte **přidejte metodu** z **bezpečnostní údaje**  stránky.

    ![Stránka informace o zabezpečení s zvýrazněnou možností přidat – metoda](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na **přidejte metodu** stránce **bezpečnostní otázky** z rozevíracího seznamu a pak vyberte **přidat**.

    ![Přidat metodu pole s vybraných bezpečnostních otázek](media/security-info/securityinfo-myprofile-addquestions.png)

4. Na **bezpečnostní otázky** stránce, zvolte a zodpovězení bezpečnostních otázek a pak vyberte **Uložit**.

    ![Přidat telefonní číslo a zvolte možnost telefonní hovory](media/security-info/securityinfo-myprofile-securityquestions.png)

    Vaše bezpečnostní údaje se aktualizuje a bezpečnostní otázky můžete použít k ověření vaší identity při použití resetování hesla.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Odstranit bezpečnostní otázky z metody informace o zabezpečení
Pokud už nechcete použít bezpečnostní otázky jako metodu informace o zabezpečení, můžete odebrat z **bezpečnostní údaje** stránky.

>[!Important]
>Pokud omylem odstraníte své bezpečnostní otázky, neexistuje žádný způsob, jak ji vrátit. Budete muset znovu, přidejte metodu podle kroků v [nastavil bezpečnostní otázky](#set-up-your-security-questions-from-the-security-info-page) části tohoto článku.

### <a name="to-delete-your-security-questions"></a>Chcete odstranit své bezpečnostní otázky

1. Na **bezpečnostní údaje** stránky, vyberte **odstranit** odkaz **bezpečnostní otázky** možnost.

    ![Odkaz na metodu phone odstranit z bezpečnostní údaje](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. Vyberte **Ano** ze seznamu potvrzení k odstranění vašich **bezpečnostní otázky**. Po odstranění své bezpečnostní otázky, metoda je odebrána ze svých bezpečnostních údajů a dané zařízení zmizí z **bezpečnostní údaje** stránky.

## <a name="additional-security-info-methods"></a>Další bezpečnostní údaje metody
Máte další možnosti pro jak kontakty vaší organizace k ověření své identity na základě toho, co jste se snažíte udělat. Mezi možnosti patří:

- **Ověřovací aplikaci.** Stáhnout a použít ověřovací aplikaci získat oznámení o schválení nebo kódu náhodně generované schválení pro dvoustupňové ověření nebo heslo resetovat. Podrobné pokyny o tom, jak nastavit a začít používat aplikaci Microsoft Authenticator, naleznete v tématu [nastavení bezpečnostní údaje pro použití ověřovací aplikaci](security-info-setup-auth-app.md).

- **Text mobilní zařízení.** Zadejte číslo svého mobilního zařízení a dostat zprávu SMS, kód budete používat pro dvoustupňové ověřování nebo heslo resetovat. Podrobné pokyny o tom, jak ověřit vaši identitu pomocí textové zprávy (SMS), najdete v části [upravit informace o zabezpečení tak, aby pomocí textové zprávy (SMS)](security-info-setup-text-msg.md).

- **Mobilní zařízení nebo pracovní telefonní hovor.** Zadejte číslo svého mobilního zařízení a získat telefonní hovor pro dvoustupňové ověření nebo heslo resetovat. Podrobné pokyny o tom, jak ověřit vaši identitu s telefonním číslem, naleznete v tématu [nastavení bezpečnostní údaje pro použití telefonní hovory](security-info-setup-phone-number.md).

- **E-mailovou adresu.** Zadejte svou pracovní nebo školní e-mailovou adresu, dostanete e-mail pro resetování hesla. Tato možnost není dostupná pro dvoustupňové ověřování. Podrobné pokyny o tom, jak nastavit e-mailu, najdete v části [nastavení bezpečnostní údaje pro použití e-mailu](security-info-setup-email.md).
   
    >[!Note]
    >Pokud chybí některé z těchto možností, je pravděpodobně, protože vaše organizace nepovoluje tyto metody. Pokud je to tento případ, budete muset zvolit metodu k dispozici nebo požádejte správce o další pomoc.

## <a name="next-steps"></a>Další postup

- Resetování hesla, pokud jste ztratíte nebo zapomenete, z [portál pro resetování hesel](https://passwordreset.microsoftonline.com/) nebo postupujte podle kroků v [obnovit heslo pracovního nebo školního](user-help-reset-password.md) článku.

- Získejte řešení potíží, tipy a nápovědu pro problémy s přihlášením v [nemůžete se přihlásit ke svému účtu Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) článku.