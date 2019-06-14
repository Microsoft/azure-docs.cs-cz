---
title: Nastavení informací zabezpečení (preview) pro použití e-mailová adresa – Azure Active Directory | Dokumentace Microsoftu
description: Jak nastavit své bezpečnostní údaje ověřit svoji identitu pomocí e-mailovou adresu.
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
ms.openlocfilehash: 720aafac79a67f64b0974dba0dd60c6aa24a8c54
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60480596"
---
# <a name="set-up-security-info-preview-to-use-your-email-address"></a>Nastavení informací zabezpečení (preview) pro použití e-mailovou adresu
Můžete následujícím postupem přidáte, že vaše heslo resetováno metoda. Po nastavení tím prvním, můžete se vrátit k **bezpečnostní údaje** stránku chcete přidat, aktualizovat nebo odstranit informace o zabezpečení.

Po nastavení vaše heslo resetováno metodu, musíte také nastavit vaše metoda dvojúrovňového ověřování pomocí [ověřovací aplikaci](security-info-setup-auth-app.md), [zasílání textových zpráv](security-info-setup-text-msg.md), nebo [telefonního hovoru](security-info-setup-phone-number.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Nastavení e-mailovou adresu, na stránce informace o zabezpečení
V závislosti na nastavení vaší organizace je možné použít e-mailovou adresu jako jeden z vašich metodách informace o zabezpečení.

>[!Note]
>Doporučujeme používat e-mailovou adresu, která nevyžaduje, aby síťové heslo pro přístup k. Pokud nevidíte možnost e-mailu, je možné, že vaše organizace nepovoluje použití e-mailu k ověření. Pokud je to tento případ, budete muset zvolit jinou metodu, nebo požádejte správce o další pomoc.

### <a name="to-set-up-your-email-address"></a>Nastavení e-mailovou adresu

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu a potom přejděte k vaší https://myprofile.microsoft.com/ stránky.

    ![Můj profil stránky zobrazující zvýrazněný odkazy informace o zabezpečení](media/security-info/securityinfo-myprofile-page.png)

2. Vyberte **bezpečnostní údaje** v levém navigačním podokně nebo z odkazu v **bezpečnostní údaje** blokovat a pak vyberte **přidejte metodu** z **bezpečnostní údaje**  stránky.

    ![Stránka informace o zabezpečení s zvýrazněnou možností přidat – metoda](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na **přidejte metodu** stránce **e-mailu** z rozevíracího seznamu a pak vyberte **přidat**.

    ![Přidat metodu pole pomocí e-mailu vybraný](media/security-info/securityinfo-myprofile-addemail.png)

4. Na **e-mailu** zadejte e-mailovou adresu (například alain@gmail.com) a pak vyberte **Další**.

    ![Přidat telefonní číslo a zvolte možnost telefonní hovory](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Tato e-mailová adresa nemůže být pracovní nebo školní e-mailu.

5. Zadejte kód poslaný na zadaný e-mailovou adresu a pak vyberte **Další**.

    ![Přidat telefonní číslo a zvolte text zprávy](media/security-info/securityinfo-myprofile-emailcode.png)

    Vaše bezpečnostní údaje se aktualizuje a e-mailovou adresu můžete použít k ověření vaší identity při použití resetování hesla.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Odstranit e-mailovou adresu z vaší metody informace o zabezpečení
Pokud nechcete použít e-mailovou adresu jako metodu informace o zabezpečení, můžete ho odebrat **bezpečnostní údaje** stránky.

>[!Important]
>Pokud omylem odstraníte e-mailovou adresu, neexistuje žádný způsob, jak ji vrátit. Budete muset znovu, přidejte metodu podle kroků v [nastavení e-mailovou adresu](#set-up-your-email-address-from-the-security-info-page) části tohoto článku.

### <a name="to-delete-your-email-address"></a>Chcete-li odstranit e-mailovou adresu

1. Na **bezpečnostní údaje** stránky, vyberte **odstranit** odkaz **e-mailu** možnost.

    ![Odkaz na metodu phone odstranit z bezpečnostní údaje](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Vyberte **Ano** ze seznamu potvrzení odstranění **e-mailu** účtu. Po e-mailový účet se odstraní, odebere se ze svých bezpečnostních údajů a dané zařízení zmizí z **bezpečnostní údaje** stránky.

## <a name="additional-security-info-methods"></a>Další bezpečnostní údaje metody
Máte další možnosti pro jak kontakty vaší organizace k ověření své identity na základě toho, co jste se snažíte udělat. Mezi možnosti patří:

- **Ověřovací aplikaci.** Stáhnout a použít ověřovací aplikaci získat oznámení o schválení nebo kódu náhodně generované schválení pro dvoustupňové ověření nebo heslo resetovat. Podrobné pokyny o tom, jak nastavit a začít používat aplikaci Microsoft Authenticator, naleznete v tématu [nastavení bezpečnostní údaje pro použití ověřovací aplikaci](security-info-setup-auth-app.md).

- **Text mobilní zařízení.** Zadejte číslo svého mobilního zařízení a dostat zprávu SMS, kód budete používat pro dvoustupňové ověřování nebo heslo resetovat. Podrobné pokyny o tom, jak ověřit vaši identitu pomocí textové zprávy (SMS), najdete v části [upravit informace o zabezpečení tak, aby pomocí textové zprávy (SMS)](security-info-setup-text-msg.md).

- **Mobilní zařízení nebo pracovní telefonní hovor.** Zadejte číslo svého mobilního zařízení a získat telefonní hovor pro dvoustupňové ověření nebo heslo resetovat. Podrobné pokyny o tom, jak ověřit vaši identitu s telefonním číslem, naleznete v tématu [nastavení bezpečnostní údaje pro použití telefonní hovory](security-info-setup-phone-number.md).

- **Bezpečnostní otázky.** Odpověď na nějakou bezpečnostní otázky vytvořený vaším správcem ve vaší organizaci. Tato možnost je pouze k dispozici pro resetování hesla a ne pro dvoustupňové ověřování. Podrobné pokyny o tom, jak nastavit své bezpečnostní otázky, najdete v článku [nastavení bezpečnostní údaje na použít bezpečnostní otázky](security-info-setup-questions.md) článku.
    
    >[!Note]
    >Pokud chybí některé z těchto možností, je pravděpodobně, protože vaše organizace nepovoluje tyto metody. Pokud je to tento případ, budete muset zvolit metodu k dispozici nebo požádejte správce o další pomoc.

## <a name="next-steps"></a>Další postup

- Resetování hesla, pokud jste ztratíte nebo zapomenete, z [portál pro resetování hesel](https://passwordreset.microsoftonline.com/) nebo postupujte podle kroků v [obnovit heslo pracovního nebo školního](user-help-reset-password.md) článku.

- Získejte řešení potíží, tipy a nápovědu pro problémy s přihlášením v [nemůžete se přihlásit ke svému účtu Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) článku.
