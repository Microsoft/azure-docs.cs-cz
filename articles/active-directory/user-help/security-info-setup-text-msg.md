---
title: Nastavte si bezpečnostní údaje (preview) použití zasílání textových zpráv – Azure Active Directory | Dokumentace Microsoftu
description: Jak nastavit své bezpečnostní údaje ověřit svoji identitu pomocí zasílání textových zpráv a vašeho mobilního zařízení.
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
ms.openlocfilehash: 00236a810fb85be6af914021acec2d2b87698e1d
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588581"
---
# <a name="set-up-security-info-preview-to-use-text-messaging"></a>Nastavení informací zabezpečení (preview) pro použití zasílání textových zpráv
Provedením následujících kroků pro přidání dvoufaktorového ověřování a metody pro resetování hesla. Po nastavení tím prvním, můžete se vrátit k **bezpečnostní údaje** stránku chcete přidat, aktualizovat nebo odstranit informace o zabezpečení.

Pokud se zobrazí výzva k nastavení ihned po přihlášení ke svému pracovnímu nebo školnímu účtu, najdete v podrobné kroky [nastavit své bezpečnostní údaje na přihlašovací stránce řádku](security-info-setup-signin.md) článku.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Pokud nevidíte možnost Telefon, je možné, že vaše organizace nepovoluje budete moci použít telefonní číslo pro ověření. V takovém případě budete muset zvolit jinou metodu, nebo požádejte správce o další pomoc.

## <a name="set-up-text-messages-from-the-security-info-page"></a>Nastavení textových zpráv na stránce informace o zabezpečení
V závislosti na nastavení vaší organizace je možné používat textové zprávy jako jeden z vašich metodách informace o zabezpečení. Možnost text zprávy je součástí variantu pro telefon, abyste budete všechno, co se stejným způsobem, jaký byste nastavit zadání telefonního čísla, ale namísto toho, aby Microsoft volání, vyberete možnost používat textové zprávy.

>[!Note]
>Pokud chcete přijímat telefonní hovor místo textovou zprávu, postupujte podle kroků v [nastavení bezpečnostní údaje pro použití telefonní hovory](security-info-setup-phone-number.md) článku.

### <a name="to-set-up-text-messages"></a>Nastavení textových zpráv

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu a potom přejděte k vaší https://myprofile.microsoft.com/ stránky.

    ![Můj profil stránky zobrazující zvýrazněný odkazy informace o zabezpečení](media/security-info/securityinfo-myprofile.png)

2. Vyberte **bezpečnostní údaje** v levém navigačním podokně nebo z odkazu v **bezpečnostní údaje** blokovat a pak vyberte **přidejte metodu** z **bezpečnostní údaje**  stránky.

    ![Stránka informace o zabezpečení s zvýrazněnou možností přidat – metoda](media/security-info/securityinfo-myprofile-addmethod.png)

3. Na **přidejte metodu** stránce **Phone** z rozevíracího seznamu a pak vyberte **přidat**.

    ![Přidat metodu pole s telefonem vybrané](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Na **Phone** stránky, zadejte telefonní číslo pro vaše mobilní zařízení, zvolte **Poslat mi SMS kód**a pak vyberte **Další**.

    ![Přidat telefonní číslo a zvolte text zprávy](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. Zadejte kód odesílat prostřednictvím textovou zprávu na vaše mobilní zařízení a pak vyberte **Další**.

    ![Přidat telefonní číslo a zvolte text zprávy](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    Změny stránky zobrazíte úspěchu.

    ![Oznámením o úspěšné připojení telefonní číslo, obdržet zpráv text a váš účet](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Aktualizovat svoje bezpečnostní údaje a můžete použít textové zprávy při použití dvoustupňové ověření nebo heslo resetovat ověřit vaši identitu. Pokud chcete změnit text vaše výchozí metoda pro zasílání zpráv, najdete v článku [změnit výchozí metodu informace o zabezpečení](#change-your-default-security-info-method) části tohoto článku.

## <a name="delete-text-messaging-from-your-security-info-methods"></a>Odstranit textové zprávy z vaší metody informace o zabezpečení
Pokud už nechcete používat textové zprávy jako metoda informace o zabezpečení, můžete ho odebrat **bezpečnostní údaje** stránky.

>[!Important]
>Pokud textové zprávy omylem odstraníte, neexistuje žádný způsob, jak ji vrátit. Budete muset znovu, přidejte metodu podle kroků v [nastavit text zprávy](#set-up-text-messages-from-the-security-info-page) části tohoto článku.

### <a name="to-delete-text-messaging"></a>Chcete-li odstranit zasílání textových zpráv

1. Na **bezpečnostní údaje** stránky, vyberte **odstranit** odkaz **Phone** možnost.

    ![Odkaz na odstranění telefonu a textové zprávy metody z bezpečnostní údaje](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Vyberte **Ano** ze seznamu potvrzení odstranění **Phone** číslo. Po odstranění své telefonní číslo, odebere se ze svých bezpečnostních údajů a dané zařízení zmizí z **bezpečnostní údaje** stránky. Pokud **Phone** je vaše výchozí metoda, výchozí hodnota se změní na jinou metodu k dispozici.

## <a name="change-your-default-security-info-method"></a>Změnit výchozí metodu informace o zabezpečení
Pokud chcete textové zprávy na výchozí metodu použitou při přihlášení k vašemu pracovního nebo školního účtu pomocí dvojúrovňového ověřování, nebo pro žádosti o resetování hesla, můžete ho z nastavit **bezpečnostní údaje** stránky.

### <a name="to-change-your-default-security-info-method"></a>Chcete-li změnit výchozí metodu informace o zabezpečení

1. Na **bezpečnostní údaje** stránky, vyberte **změnu** odkaz **výchozí metody přihlašování** informace.

    ![Změnit vazbu pro výchozí přihlašovací – metoda](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Vyberte **Phone – text (*_your_phone_number_*)** z rozevíracího seznamu dostupných metod a pak vyberte **potvrdit**.

    ![Vyberte metodu pro přihlášení výchozí](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    Výchozí metoda použitá pro přihlašování změny **Phone – text (*_your_phone_number_*)**.

## <a name="additional-security-info-methods"></a>Další bezpečnostní údaje metody
Máte další možnosti pro jak kontakty vaší organizace k ověření své identity na základě toho, co jste se snažíte udělat. Mezi možnosti patří:

- **Ověřovací aplikaci.** Stáhnout a použít ověřovací aplikaci získat oznámení o schválení nebo kódu náhodně generované schválení pro dvoustupňové ověření nebo heslo resetovat. Podrobné pokyny o tom, jak nastavit a začít používat aplikaci Microsoft Authenticator, naleznete v tématu [nastavení bezpečnostní údaje pro použití ověřovací aplikaci](security-info-setup-auth-app.md).

- **Mobilní zařízení nebo pracovní telefonní hovor.** Zadejte číslo svého mobilního zařízení a získat telefonní hovor pro dvoustupňové ověření nebo heslo resetovat. Podrobné pokyny o tom, jak ověřit vaši identitu s telefonním číslem, naleznete v tématu [nastavení bezpečnostní údaje pro použití telefonní hovory](security-info-setup-phone-number.md).

- **E-mailovou adresu.** Zadejte svou pracovní nebo školní e-mailovou adresu, dostanete e-mail pro resetování hesla. Tato možnost není dostupná pro dvoustupňové ověřování. Podrobné pokyny o tom, jak nastavit e-mailu, najdete v části [nastavení bezpečnostní údaje pro použití e-mailu](security-info-setup-email.md).

- **Bezpečnostní otázky.** Odpověď na nějakou bezpečnostní otázky vytvořený vaším správcem ve vaší organizaci. Tato možnost je pouze k dispozici pro resetování hesla a ne pro dvoustupňové ověřování. Podrobné pokyny o tom, jak nastavit své bezpečnostní otázky, najdete v článku [nastavení bezpečnostní údaje na použít bezpečnostní otázky](security-info-setup-questions.md) článku.
    
    >[!Note]
    >Pokud chybí některé z těchto možností, je pravděpodobně, protože vaše organizace nepovoluje tyto metody. Pokud je to tento případ, budete muset zvolit metodu k dispozici nebo požádejte správce o další pomoc.

## <a name="next-steps"></a>Další postup

- Resetování hesla, pokud jste ztratíte nebo zapomenete, z [portál pro resetování hesel](https://passwordreset.microsoftonline.com/) nebo postupujte podle kroků v [obnovit heslo pracovního nebo školního](user-help-reset-password.md) článku.

- Získejte řešení potíží, tipy a nápovědu pro problémy s přihlášením v [nemůžete se přihlásit ke svému účtu Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) článku.