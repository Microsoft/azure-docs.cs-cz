---
title: Nastavení mobilního zařízení jako dvoufaktorové metody ověření – Azure Active Directory | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nastavit mobilní zařízení jako dvoufaktorovou metodu ověření.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: b0bfaa87c77ba9fff9f6605c1989e48ffbc3fb35
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062502"
---
# <a name="set-up-a-mobile-device-as-your-two-factor-verification-method"></a>Nastavení mobilního zařízení jako dvoufaktorové metody ověření

Mobilní zařízení můžete nastavit tak, aby působilo jako dvoufaktorová metoda ověření. Váš mobilní telefon může buď přijímat textovou zprávu s ověřovacím kódem, nebo telefonní hovor.

>[!Note]
> Pokud je možnost ověřovacího telefonu zašedlá, je možné, že vaše organizace neumožňuje používat telefonní číslo nebo textovou zprávu k ověření. V takovém případě budete muset vybrat jinou metodu nebo požádat správce o další pomoc.

## <a name="set-up-your-mobile-device-to-use-a-text-message-as-your-verification-method"></a>Nastavení mobilního zařízení tak, aby používalo textovou zprávu jako metodu ověření

1. Na stránce **Další ověření zabezpečení** vyberte možnost Ověřování **telefonu** v části **Krok 1: Jak vás máme kontaktovat,** vrozeném seznamu vyberte zemi nebo oblast a zadejte číslo telefonu mobilního zařízení.

2. Vyberte **Odeslat mi kód textovou zprávou** z oblasti **Metoda** a pak vyberte **Další**.

    ![Další stránka pro ověření zabezpečení s ověřovacím telefonem a textovou zprávou](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message.png)

3. Zadejte ověřovací kód z textové zprávy odeslané od microsoftu do **kroku 2: Odeslali jsme textovou zprávu do** oblasti telefonu a pak vyberte **Ověřit**.

    ![Další stránka pro ověření zabezpečení s ověřovacím telefonem a textovou zprávou](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message-test.png)

4. Krok **3: Pokračujte v používání stávající oblasti aplikací, zkopírujte** zadaný hesel aplikace a vložte ho někam do bezpečí.

    ![Oblast hesel aplikací na stránce Další ověření zabezpečení](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Informace o tom, jak používat heslo aplikace ve starších aplikacích, najdete v [tématu Správa hesel aplikací](multi-factor-authentication-end-user-app-passwords.md). Hesla aplikací stačí, když nadále používáte starší aplikace, které nepodporují dvoufaktorové ověření.

5. Vyberte **Done** (Hotovo).

## <a name="set-up-your-mobile-device-to-receive-a-phone-call"></a>Nastavení mobilního zařízení pro příjem telefonního hovoru

1. Na stránce **Další ověření zabezpečení** vyberte možnost Ověřování **telefonu** v části **Krok 1: Jak vás máme kontaktovat,** vrozeném seznamu vyberte zemi nebo oblast a zadejte číslo telefonu mobilního zařízení.

2. V oblasti **Metoda** **vyberte Volat** a pak vyberte **Další**.

    ![Další stránka pro ověření zabezpečení s autentizačním telefonem a telefonním hovorem](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call.png)

3. Obdržíte telefonní hovor od společnosti Microsoft, s dotazem, že stisknete libru (#) podepsat na svém mobilním zařízení k ověření vaší identity.

    ![Testování zadaného telefonního čísla](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call-test.png)

4. Krok **3: Pokračujte v používání stávající oblasti aplikací, zkopírujte** zadaný hesel aplikace a vložte ho někam do bezpečí.

    ![Oblast hesel aplikací na stránce Další ověření zabezpečení](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Informace o tom, jak používat heslo aplikace ve starších aplikacích, najdete v [tématu Správa hesel aplikací](multi-factor-authentication-end-user-app-passwords.md). Hesla aplikací stačí, když nadále používáte starší aplikace, které nepodporují dvoufaktorové ověření.

5. Vyberte **Done** (Hotovo).

## <a name="next-steps"></a>Další kroky

Po nastavení dvoufaktorové metody ověření můžete přidat další metody, spravovat nastavení a hesla aplikací, přihlásit se nebo získat pomoc s některými běžnými problémy souvisejícími s dvoufaktorovým ověřováním.

- [Správa nastavení dvoufaktorových metod ověření](multi-factor-authentication-end-user-manage-settings.md)

- [Správa hesel aplikací](multi-factor-authentication-end-user-app-passwords.md)

- [Přihlášení pomocí dvoufaktorového ověření](multi-factor-authentication-end-user-signin.md)

- [Získání nápovědy k dvoufaktorovému ověření](multi-factor-authentication-end-user-troubleshoot.md)
