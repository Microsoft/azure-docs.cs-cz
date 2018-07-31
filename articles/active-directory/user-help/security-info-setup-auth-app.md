---
title: Upravit informace o zabezpečení tak, aby pomocí ověřovací aplikace – Azure Active Directory | Dokumentace Microsoftu
description: Nastavte svoje bezpečnostní údaje ověřit svoji identitu pomocí aplikace Microsoft Authenticator.
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: bd62ff03c3158743337ce958eb1bda7c7bd2a7f6
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39348256"
---
# <a name="set-up-security-info-to-use-an-authenticator-app-preview"></a>Upravit informace o zabezpečení tak, aby pomocí ověřovací aplikace (preview)

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Nastavení bezpečnostních údajů vyžaduje, abyste pro přihlášení ke svému pracovnímu nebo školnímu účtu a pak dokončete proces registrace. Pokud jste dosud nenastavili své bezpečnostní údaje, budete vyzváni k tomu teď.

## <a name="set-up-the-microsoft-authenticator-app"></a>Nastavit aplikaci Microsoft Authenticator

V závislosti na nastavení vaší organizace můžete být vyzváni k nastavení aplikace Microsoft Authenticator při přihlášení. V opačném případě k nastavení aplikace Microsoft Authenticator bezpečnostních údajů začít, postupujte podle kroků v [spravovat svoje bezpečnostní údaje](security-info-manage-settings.md).

Ke stažení a další informace o aplikaci Microsoft Authenticator, naleznete v tématu [Začínáme s aplikací Microsoft Authenticator](microsoft-authenticator-app-how-to.md).

>[!Note]
>Pokud nechcete použít aplikaci Microsoft Authenticator, můžete různé aplikace během nastavení. Tento článek používá aplikaci Microsoft Authenticator. Pokud nevidíte možnost aplikace authenticator, je možné, že vaše organizace nepovoluje budete moci použít ověřovací aplikaci pro ověření. Pokud je to tento případ, budete muset zvolit jinou metodu, nebo požádejte správce o další pomoc.

### <a name="to-use-the-microsoft-authenticator-app"></a>K používání aplikace Microsoft Authenticator

1. Vyberte **aplikace Authenticator** možnost.

    **Získat aplikaci** průvodce se zobrazí.

    ![Získejte Průvodce aplikací, úvodní obrazovka](media/security-info/security-info-auth-app-wizard.png)

    Pokud nechcete použít aplikaci Microsoft Authenticator, můžete kliknout **chci používat jinou ověřovací aplikaci** propojit **získat aplikaci** obrazovky.

2. Po instalaci aplikace Microsoft Authenticator, vyberte **Další**.

    Pokud budete vyzváni, povolit oznámení, přidat nový účet a potom vyberte **pracovní nebo školní účet**.

3. Vyberte **Další**.

    **Naskenovat kód QR** obrazovky.

    ![Skenování kódu QR pomocí ověřovací aplikace](media/security-info/security-info-scan-qr.png)

4. Otevřete aplikaci Microsoft Authenticator, vyberte **přidat účet** z **přizpůsobení a řízení** ikonu v pravém horním rohu a pak vyberte **pracovní nebo školní účet**. 

5. Pokud budete mít aplikaci čtečky kód QR, naskenujte uvedený kód. Pokud nemáte aplikaci čtečky kódu, můžete vybrat **nejde kontrolovat na odkaz kódu QR** a ručně zadat kód a adresu URL do aplikace Microsoft Authenticator.

6. Pomocí aplikace Microsoft Authenticator schválit oznámení, aby aplikaci aktivoval.

    Vaše bezpečnostní údaje se aktualizuje a ověřit vaši identitu, při použití dvoustupňové ověření nebo samoobslužné resetování hesla pomocí aplikace Microsoft Authenticator.

## <a name="additional-security-info-options"></a>Další informace o možnosti zabezpečení

Máte další možnosti pro jak kontakty vaší organizace k ověření své identity na základě toho, co jste se snažíte udělat. Mezi možnosti patří:

- **Text mobilní zařízení.** Zadejte číslo svého mobilního zařízení a dostat zprávu SMS, kód budete používat pro dvoustupňové ověřování nebo heslo resetovat. Podrobné pokyny o tom, jak ověřit vaši identitu pomocí textové zprávy (SMS), najdete v části [upravit informace o zabezpečení tak, aby pomocí textové zprávy (SMS)](security-info-setup-text-msg.md).

- **Mobilní zařízení nebo pracovní telefonní hovor.** Zadejte číslo svého mobilního zařízení a získat telefonní hovor pro dvoustupňové ověření nebo heslo resetovat. Podrobné pokyny o tom, jak ověřit vaši identitu s telefonním číslem, naleznete v tématu [nastavení bezpečnostní údaje pro použití telefonní hovory](security-info-setup-phone-number.md).

- **E-mailovou adresu.** Zadejte svou pracovní nebo školní e-mailovou adresu, dostanete e-mail pro resetování hesla. Tato možnost není dostupná pro dvoustupňové ověřování. Podrobné pokyny o tom, jak nastavit e-mailu, najdete v části [nastavení bezpečnostní údaje pro použití e-mailu](security-info-setup-email.md).

- **Bezpečnostní otázky.** Odpověď na nějakou bezpečnostní otázky vytvořený vaším správcem ve vaší organizaci. Tato možnost je pouze k dispozici pro resetování hesla a ne pro dvoustupňové ověřování. Podrobné pokyny o tom, jak nastavit své bezpečnostní otázky, najdete v článku [nastavení bezpečnostní údaje na použít bezpečnostní otázky](security-info-setup-questions.md) článku.
    
    >[!Note]
    >Pokud chybí některé z těchto možností, je pravděpodobně, protože vaše organizace nepovoluje tyto metody. Pokud je to tento případ, budete muset zvolit metodu k dispozici nebo požádejte správce o další pomoc.

## <a name="next-steps"></a>Další postup

- Pokud je potřeba aktualizovat svoje bezpečnostní údaje, postupujte podle pokynů [spravovat svoje bezpečnostní údaje](security-info-manage-settings.md) článku.

- Resetování hesla, pokud jste ztratíte nebo zapomenete, z [portál pro resetování hesel](https://passwordreset.microsoftonline.com/) nebo postupujte podle kroků v [obnovit heslo pracovního nebo školního](user-help-reset-password.md) článku.

- Získejte řešení potíží, tipy a nápovědu pro problémy s přihlášením v [nemůžete se přihlásit ke svému účtu Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) článku.