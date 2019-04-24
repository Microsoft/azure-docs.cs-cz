---
title: Nastavte informace o zabezpečení (preview) z vaší přihlášení řádku – Azure Active Directory | Dokumentace Microsoftu
description: Jak nastavit bezpečnostní údaje pro váš pracovní nebo školní účet, pokud se zobrazí výzva k z přihlašovací stránku vaší organizace.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: overview
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9c6faff10f68d720bc3c86a191e4cd1b1f9abdc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60475171"
---
# <a name="set-up-your-security-info-preview-from-the-sign-in-page-prompt"></a>Nastavení bezpečnostních údajů (ve verzi preview) z příkazového řádku přihlašovací stránky
Pokud se zobrazí výzva k nastavení bezpečnostních údajů ihned po přihlášení ke svému pracovnímu nebo školnímu účtu, můžete následujícím postupem.

Tato výzva zobrazí jenom Pokud jste nenastavili bezpečnostní údaje, které vaše organizace vyžaduje. Pokud jste dříve nastavili svoje bezpečnostní údaje, ale chcete provést změny, provedením kroků v různých články s postupy založených na volání metody. Další informace najdete v tématu [přidat nebo aktualizovat vaše informace o zabezpečení – přehled](security-info-add-update-methods-overview.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="sign-in-to-your-work-or-school-account"></a>Přihlaste se ke svému pracovnímu nebo školnímu účtu
Po přihlášení ke svému pracovnímu nebo školnímu účtu, zobrazí se vám výzva s dotazem, abychom vám poskytli informace předtím, než umožňuje přístup k vašemu účtu.

![Výzva s dotazem, další informace](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>Nastavte si svoje bezpečnostní údaje, pomocí Průvodce
Postupujte podle těchto pokynů nastavte si svoje bezpečnostní údaje pro váš pracovní nebo školní účet z příkazového řádku.

>[!Important]
>Toto je pouze příklad procesu. V závislosti na požadavcích vaší organizace může mít správce nastavit jinou ověřovací metody, které je potřeba nastavit během tohoto procesu. V tomto příkladu jsme se vyžaduje dvě metody, aplikaci Microsoft Authenticator a číslo mobilního telefonu pro ověření za přijaté hovory nebo textové zprávy.

1. Po výběru **Další** z příkazového řádku, **zachovat Průvodce zabezpečený účet** zobrazí první metoda správce a organizace vyžadují, abyste nastavení. V tomto příkladu je aplikace Microsoft Authenticator.

   > [!Note]
   > Pokud chcete použít aplikaci authenticator než aplikace Microsoft Authenticator, vyberte **chci používat jinou ověřovací aplikaci** odkaz.
   > 
   > Pokud vaše organizace vám umožní vybrat si jinou metodu kromě ověřovací aplikaci, můžete vybrat **budu chtít nastavit odkazem na jinou metodu**.

    ![Zachovat Průvodce zabezpečený účet, stránkou ověřování aplikace ke stažení](media/security-info/securityinfo-prompt-get-auth-app.png)

2. Vyberte **Stáhnout** chcete stáhnout a nainstalovat aplikaci Microsoft Authenticator na vašem mobilním zařízení a potom vyberte **Další**. Další informace o tom, jak stáhnout a nainstalovat aplikaci najdete v tématu [stáhněte a nainstalujte aplikaci Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Zachovat Průvodce zabezpečený účet, zobrazuje authenticator sady stránku vašeho účtu](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. Zůstat na **nastavili svůj účet** stránce během nastavení aplikace Microsoft Authenticator na vašem mobilním zařízení.

4. Otevřete aplikaci Microsoft Authenticator, výběrem ji povolte oznámení (Pokud se zobrazí výzva), vyberte **přidat účet** z **přizpůsobení a řízení** ikonu v pravém horním rohu a pak vyberte **pracovní nebo školní účet**.

5. Zpět **nastavili svůj účet** stránky ve vašem počítači a potom vyberte **Další**.

    **Naskenovat kód QR** se zobrazí stránka.

    ![Skenování kódu QR pomocí ověřovací aplikace](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. Naskenujte poskytnutý kód pomocí čtečky kódu aplikace QR Microsoft Authenticator, která se nacházela na svém mobilním zařízení po vytvoření pracovní nebo školní účet v kroku 5.

    Aplikace authenticator by měla úspěšně přidat váš pracovní nebo školní účet bez nutnosti jakýchkoli dalších informací od vás. Nicméně pokud kód nelze číst čtečka kód QR, můžete vybrat **nejde kontrolovat na odkaz kódu QR** a ručně zadat kód a adresu URL do aplikace Microsoft Authenticator. Další informace o ručním přidání kód, naleznete v tématu [ručně přidat účet do aplikace](user-help-auth-app-add-account-manual.md).

7. Vyberte **Další** na **naskenovat kód QR** stránky ve vašem počítači.

    Oznámení odesílat do aplikace Microsoft Authenticator na vašem mobilním zařízení, otestovat svůj účet.

    ![Testování účtu v ověřovací aplikaci](media/security-info/securityinfo-prompt-test-app.png)

8. Schvalte oznámení v aplikaci Microsoft Authenticator a pak vyberte **Další**.

    ![Oznámením o úspěšné připojení aplikace a váš účet](media/security-info/securityinfo-prompt-auth-app-success.png).

    Vaše bezpečnostní údaje se aktualizuje a použít aplikaci Microsoft Authenticator ve výchozím nastavení při použití dvoustupňové ověření nebo heslo resetovat ověřit vaši identitu.

9. Na **Phone** stránky nastavení, vyberte, jestli chcete obdržet textovou zprávu nebo telefonní hovor a pak vyberte **Další**. Pro účely tohoto příkladu používáme textových zpráv, takže je nutné použít telefonní číslo zařízení, které můžou přijímat textové zprávy.

    ![Zahajte nastavení své telefonní číslo pro zasílání textových zpráv](media/security-info/securityinfo-prompt-text-msg.png)

    Na vaše telefonní číslo je odeslána textová zpráva. Pokud chcete získat telefonní hovor, proces je stejný. Ale obdržíte telefonický hovor s pokyny, nikoli textovou zprávu.

10. Zadejte kód poskytuje textová zpráva byla odeslána na vaše mobilní zařízení a pak vyberte **Další**.

    ![Testovat váš účet pomocí textové zprávy.](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. Zkontrolujte oznámením o úspěšné a pak vyberte **provádí**.

    ![Oznámením o úspěšné](media/security-info/securityinfo-prompt-call-answered-success.png)

    Vaše bezpečnostní údaje se aktualizuje a ověřit vaši identitu, při použití dvoustupňové ověření nebo heslo resetovat pomocí textové zprávy jako záložní metodu.

12. Zkontrolujte **úspěch** stránce ověřit, že jste úspěšně nastavili aplikaci Microsoft Authenticator a Telefon (textovou zprávu nebo telefonní hovor) metody pro vaše bezpečnostní údaje a pak vyberte **provádí** .

    ![Stránka průvodce úspěšně dokončila.](media/security-info/securityinfo-prompt-setup-success.png)

## <a name="next-steps"></a>Další postup

- Chcete-li změnit, odstranit, nebo výchozí zabezpečení informace o metodách aktualizace najdete:

    - [Nastavte si bezpečnostní údaje pro ověřovací aplikaci](security-info-setup-auth-app.md).

    - [Nastavte si bezpečnostní údaje pro zasílání textových zpráv](security-info-setup-text-msg.md).

    - [Nastavení informací o zabezpečení pro použití telefonní hovory](security-info-setup-phone-number.md).

    - [Nastavení informací o zabezpečení pro použití e-mailu](security-info-setup-email.md).

    - [Nastavte si bezpečnostní údaje použít předdefinované bezpečnostní otázky](security-info-setup-questions.md).

- Informace o tom, jak se přihlásit pomocí zadanou metodu naleznete v tématu [přihlášení](user-help-sign-in.md).

- Resetování hesla, pokud jste ztratíte nebo zapomenete, z [portál pro resetování hesel](https://passwordreset.microsoftonline.com/) nebo postupujte podle kroků v [obnovit heslo pracovního nebo školního](user-help-reset-password.md) článku.

- Získejte řešení potíží, tipy a nápovědu pro problémy s přihlášením v [nemůžete se přihlásit ke svému účtu Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) článku.