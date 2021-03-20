---
title: Nastavení mobilního zařízení jako metody dvou faktorů ověřování – Azure Active Directory | Microsoft Docs
description: Naučte se, jak nastavit mobilní zařízení jako metodu dvou faktorů ověřování.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: 40af9ac692a226b12f984db447db9635cba655e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91530251"
---
# <a name="set-up-a-mobile-device-as-your-two-factor-verification-method"></a>Nastavení mobilního zařízení jako metody ověřování dvou faktorů

Mobilní zařízení můžete nastavit tak, aby fungovalo jako metoda dvou faktorů ověřování. Váš mobilní telefon může buď přijmout textovou zprávu s ověřovacím kódem, nebo s telefonním hovorem.

>[!Note]
> Pokud je možnost telefon pro ověřování šedá, je možné, že vaše organizace neumožňuje použít pro ověření telefonní číslo nebo textovou zprávu. V takovém případě budete muset vybrat jinou metodu nebo požádat správce o další nápovědu.

## <a name="set-up-your-mobile-device-to-use-a-text-message-as-your-verification-method"></a>Nastavte si mobilní zařízení, aby jako metodu ověřování používalo textovou zprávu.

1. Na stránce **Další ověření zabezpečení** vyberte možnost **telefon pro ověření** z **kroku 1: jak vás máme kontaktovat** oblast, v rozevíracím seznamu vyberte zemi nebo oblast a pak zadejte telefonní číslo mobilního zařízení.

2. V oblasti **Metoda** vyberte **Odeslat zprávu kód podle textové zprávy** a pak vyberte **Další**.

    ![Snímek obrazovky se stránkou "další ověření zabezpečení" s výběrem možnosti "telefon pro ověření" a "poslat mi kód pomocí textové zprávy".](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message.png)

3. Zadejte ověřovací kód z textové zprávy odeslané od Microsoftu do **kroku 2: poslali jsme vám textovou zprávu do vaší telefonní** oblasti a pak vyberete **ověřit**.

    ![Stránka Další ověření zabezpečení s ověřováním telefon a textová zpráva](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message-test.png)

4. V **kroku 3: pokračujte v oblasti používání stávajících aplikací** , zkopírujte zadané heslo aplikace a vložte ho někam do trezoru.

    ![Oblast hesel aplikací na stránce dodatečného ověření zabezpečení](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Informace o tom, jak používat heslo aplikace pro vaše starší aplikace, najdete v tématu [Správa hesel aplikací](multi-factor-authentication-end-user-app-passwords.md). Pokud budete nadále používat starší aplikace, které nepodporují dvojúrovňové ověřování, je třeba použít hesla aplikací.

5. Vyberte **Hotovo**.

## <a name="set-up-your-mobile-device-to-receive-a-phone-call"></a>Nastavení mobilního zařízení pro příjem telefonního hovoru

1. Na stránce **Další ověření zabezpečení** vyberte možnost **telefon pro ověření** z **kroku 1: jak vás máme kontaktovat** oblast, v rozevíracím seznamu vyberte zemi nebo oblast a pak zadejte telefonní číslo mobilního zařízení.

2. Vyberte možnost **zavolat mi** z oblasti **Metoda** a pak vyberte **Další**.

    ![Stránka Další ověření zabezpečení s ověřováním telefon a telefonní hovor](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call.png)

3. Od Microsoftu obdržíte telefonický hovor s výzvou, abyste si ověřili svoji identitu stisknutím křížku (#) na mobilním zařízení.

    ![Testování zadaného telefonního čísla](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call-test.png)

4. V **kroku 3: pokračujte v oblasti používání stávajících aplikací** , zkopírujte zadané heslo aplikace a vložte ho někam do trezoru.

    ![Oblast hesel aplikací na stránce dodatečného ověření zabezpečení](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Informace o tom, jak používat heslo aplikace pro vaše starší aplikace, najdete v tématu [Správa hesel aplikací](multi-factor-authentication-end-user-app-passwords.md). Pokud budete nadále používat starší aplikace, které nepodporují dvojúrovňové ověřování, je třeba použít hesla aplikací.

5. Vyberte **Hotovo**.

## <a name="next-steps"></a>Další kroky

Po nastavení dvojúrovňové metody ověřování můžete přidat další metody, spravovat nastavení a hesla aplikací, přihlašovat nebo získat pomoc s některými běžnými problémy souvisejícími s ověřováním dvou faktorů.

- [Správa nastavení dvojúrovňové metody ověřování](multi-factor-authentication-end-user-manage-settings.md)

- [Správa hesel aplikací](multi-factor-authentication-end-user-app-passwords.md)

- [Přihlášení Pomocí dvojúrovňového ověřování](multi-factor-authentication-end-user-signin.md)

- [Získat pomoc se dvojúrovňovém ověřováním](multi-factor-authentication-end-user-troubleshoot.md)
