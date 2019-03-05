---
title: Nastavení informací zabezpečení (preview) pro použití aplikace authenticator – Azure Active Directory | Dokumentace Microsoftu
description: Jak nastavit své bezpečnostní údaje ověřit svoji identitu pomocí aplikace Microsoft Authenticator.
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
ms.openlocfilehash: 336b30a8e8687b3a2bdd05a17e558f44905f135e
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338837"
---
# <a name="set-up-security-info-preview-to-use-an-authenticator-app"></a>Nastavení informací zabezpečení (preview) pro použití ověřovací aplikaci
Provedením následujících kroků pro přidání dvoufaktorového ověřování a metody pro resetování hesla. Po nastavení tím prvním, můžete se vrátit k **bezpečnostní údaje** stránku chcete přidat, aktualizovat nebo odstranit informace o zabezpečení.

Pokud se zobrazí výzva k nastavení ihned po přihlášení ke svému pracovnímu nebo školnímu účtu, najdete v podrobné kroky [nastavit své bezpečnostní údaje na přihlašovací stránce řádku](security-info-setup-signin.md) článku.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Pokud nevidíte možnost aplikace authenticator, je možné, že vaše organizace nepovoluje budete moci použít ověřovací aplikaci pro ověření. V takovém případě budete muset zvolit jinou metodu, nebo požádejte správce o další pomoc.

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Nastavit aplikaci Microsoft Authenticator na stránce informace o zabezpečení
V závislosti na nastavení vaší organizace je možné použít ověřování aplikace jako jeden z vašich metodách informace o zabezpečení. Není nutné použít aplikaci Microsoft Authenticator a můžete zvolit jinou aplikaci během procesu nastavení. Tento článek ale používá aplikaci Microsoft Authenticator. 

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Chcete-li nastavit aplikaci Microsoft Authenticator

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu a potom přejděte k vaší https://myprofile.microsoft.com/ stránky.

    ![Můj profil stránky zobrazující zvýrazněný odkazy informace o zabezpečení](media/security-info/securityinfo-myprofile-page.png)

2. Vyberte **bezpečnostní údaje** v levém navigačním podokně nebo z odkazu v **bezpečnostní údaje** blokovat a pak vyberte **přidejte metodu** z **bezpečnostní údaje**  stránky.

    ![Stránka informace o zabezpečení s zvýrazněnou možností přidat – metoda](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na **přidejte metodu** stránce **aplikace Authenticator** z rozevíracího seznamu a pak vyberte **přidat**.

    ![Přidat pole, metoda s vybraná aplikace Authenticator](media/security-info/securityinfo-myprofile-addauthapp.png)

4. Na **začít nastavením aplikace** stránce **Stáhnout** chcete stáhnout a nainstalovat aplikaci Microsoft Authenticator na vašem mobilním zařízení a potom vyberte **Další**.

    Další informace o tom, jak stáhnout a nainstalovat aplikaci najdete v tématu [stáhněte a nainstalujte aplikaci Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Začněte tím, že získáme stránka aplikace](media/security-info/securityinfo-myprofile-getauthapp.png)

    >[!Note]
    >Pokud chcete použít aplikaci authenticator než aplikace Microsoft Authenticator, vyberte **chci používat jinou ověřovací aplikaci** odkaz.
    
    >Pokud vaše organizace vám umožní vybrat si jinou metodu kromě ověřovací aplikaci, můžete vybrat **budu chtít nastavit odkazem na jinou metodu**.

5. Zůstat na **nastavili svůj účet** stránce během nastavení aplikace Microsoft Authenticator na vašem mobilním zařízení.

    ![Nastavení na stránce aplikace authenticator](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Otevřete aplikaci Microsoft Authenticator, výběrem ji povolte oznámení (Pokud se zobrazí výzva), vyberte **přidat účet** z **přizpůsobení a řízení** ikonu v pravém horním rohu a pak vyberte **pracovní nebo školní účet**.

7. Zpět **nastavili svůj účet** na stránce a pak vyberte **Další**.

    **Naskenovat kód QR** se zobrazí stránka.

    ![Skenování kódu QR pomocí ověřovací aplikace](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

6. Naskenujte poskytnutý kód pomocí čtečky kódu aplikace QR Microsoft Authenticator, která se nacházela na svém mobilním zařízení po vytvoření pracovní nebo školní účet v kroku 6.

    Aplikace authenticator by měla úspěšně přidat váš pracovní nebo školní účet bez nutnosti jakýchkoli dalších informací od vás. Nicméně pokud kód nelze číst čtečka kód QR, můžete vybrat **nejde kontrolovat na odkaz kódu QR** a ručně zadat kód a adresu URL do aplikace Microsoft Authenticator. Další informace o ručním přidání kód, naleznete v tématu [ručně přidat účet do aplikace](user-help-auth-app-add-account-manual.md).

7. Vyberte **Další** na **naskenovat kód QR** stránky na vaše.

    Oznámení odesílat do aplikace Microsoft Authenticator na vašem mobilním zařízení, otestovat svůj účet.

    ![Testování účtu v ověřovací aplikaci](media/security-info/securityinfo-myprofile-tryitauthapp.png)

8. Schvalte oznámení v aplikaci Microsoft Authenticator a pak vyberte **Další**.

    ![Oznámením o úspěšné připojení aplikace a váš účet](media/security-info/securityinfo-myprofile-successauthapp.png)

    Vaše bezpečnostní údaje se aktualizuje a použít aplikaci Microsoft Authenticator ve výchozím nastavení při použití dvoustupňové ověření nebo heslo resetovat ověřit vaši identitu.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Odstranit aplikaci authenticator z metody informace o zabezpečení
Pokud už nechcete používat vaší ověřovací aplikací jako způsob informace o zabezpečení, můžete ho odebrat **bezpečnostní údaje** stránky. Tento postup funguje pro všechny aplikace, ne jenom aplikace Microsoft Authenticator. Po odstranění aplikace, budete muset přejít do aplikace authenticator na vašem mobilním zařízení a odstraní účet.

>[!Important]
>Pokud omylem odstraníte aplikaci authenticator, neexistuje žádný způsob, jak ji vrátit. Budete muset přidat ověřovací aplikaci znovu, proveďte kroky v [nastavit ověřovací aplikaci](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) části tohoto článku.

### <a name="to-delete-the-authenticator-app"></a>Chcete-li odstranit ověřovací aplikaci

1. Na **bezpečnostní údaje** stránky, vyberte **odstranit** odkaz vedle ověřovací aplikaci.

    ![Odkaz na odstranění aplikace authenticator z bezpečnostní údaje](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Vyberte **Ano** ze seznamu potvrzení k odstranění ověřovací aplikaci. Po aplikaci authenticator se odstraní, odebere se ze svých bezpečnostních údajů a dané zařízení zmizí z **bezpečnostní údaje** stránky. Pokud aplikace authenticator je vaše výchozí metoda, výchozí hodnota se změní na jinou metodu k dispozici.

3. Otevřete aplikaci authenticator na vašem mobilním zařízení, vyberte **úpravy účtů**a pak odstraňte svůj pracovní nebo školní účet z aplikace authenticator.

    Váš účet je zcela odebrán z aplikace authenticator dvojúrovňového ověřování a žádosti o resetování hesla.

## <a name="change-your-default-security-info-method"></a>Změnit výchozí metodu informace o zabezpečení
Pokud chcete aplikaci authenticator na výchozí metodu použitou při přihlášení k vašemu pracovního nebo školního účtu pomocí dvojúrovňového ověřování, nebo pro žádosti o resetování hesla, můžete ho nastavit zabezpečení **informace** stránky.

### <a name="to-change-your-default-security-info-method"></a>Chcete-li změnit výchozí metodu informace o zabezpečení

1. Na **bezpečnostní údaje** stránky, vyberte **změnu** odkaz **výchozí metody přihlašování** informace.

    ![Změnit vazbu pro výchozí přihlašovací – metoda](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Zvolte **Microsoft Authenticator – oznámení** z rozevíracího seznamu dostupných metod. Pokud nepoužíváte aplikaci Microsoft Authenticator, vyberte **ověřovací aplikace nebo hardwarového tokenu** možnost.

    ![Vyberte metodu pro přihlášení výchozí](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Vyberte **potvrďte**.

    Výchozí metoda použitý pro změny přihlášení do aplikace Microsoft Authenticator.

## <a name="additional-security-info-methods"></a>Další bezpečnostní údaje metody
Máte další možnosti pro jak kontakty vaší organizace k ověření své identity na základě toho, co jste se snažíte udělat. Mezi možnosti patří:

- **Text mobilní zařízení.** Zadejte číslo svého mobilního zařízení a dostat zprávu SMS, kód budete používat pro dvoustupňové ověřování nebo heslo resetovat. Podrobné pokyny o tom, jak ověřit vaši identitu pomocí textové zprávy (SMS), najdete v části [upravit informace o zabezpečení tak, aby pomocí textové zprávy (SMS)](security-info-setup-text-msg.md).

- **Mobilní zařízení nebo pracovní telefonní hovor.** Zadejte číslo svého mobilního zařízení a získat telefonní hovor pro dvoustupňové ověření nebo heslo resetovat. Podrobné pokyny o tom, jak ověřit vaši identitu s telefonním číslem, naleznete v tématu [nastavení bezpečnostní údaje pro použití telefonní hovory](security-info-setup-phone-number.md).

- **E-mailovou adresu.** Zadejte svou pracovní nebo školní e-mailovou adresu, dostanete e-mail pro resetování hesla. Tato možnost není dostupná pro dvoustupňové ověřování. Podrobné pokyny o tom, jak nastavit e-mailu, najdete v části [nastavení bezpečnostní údaje pro použití e-mailu](security-info-setup-email.md).

- **Bezpečnostní otázky.** Odpověď na nějakou bezpečnostní otázky vytvořený vaším správcem ve vaší organizaci. Tato možnost je pouze k dispozici pro resetování hesla a ne pro dvoustupňové ověřování. Podrobné pokyny o tom, jak nastavit své bezpečnostní otázky, najdete v článku [nastavení bezpečnostní údaje na použít bezpečnostní otázky](security-info-setup-questions.md) článku.
    
    >[!Note]
    >Pokud chybí některé z těchto možností, je pravděpodobně, protože vaše organizace nepovoluje tyto metody. Pokud je to tento případ, budete muset zvolit metodu k dispozici nebo požádejte správce o další pomoc.

## <a name="next-steps"></a>Další postup

- Resetování hesla, pokud jste ztratíte nebo zapomenete, z [portál pro resetování hesel](https://passwordreset.microsoftonline.com/) nebo postupujte podle kroků v [obnovit heslo pracovního nebo školního](user-help-reset-password.md) článku.

- Získejte řešení potíží, tipy a nápovědu pro problémy s přihlášením v [nemůžete se přihlásit ke svému účtu Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) článku.