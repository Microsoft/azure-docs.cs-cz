---
title: Přihlášení pomocí pracovního nebo školního účtu – Azure AD
description: Naučte se přihlašovat k pracovnímu nebo školnímu účtu pomocí různých metod ověřování dvou faktorů.
services: active-directory
author: curtand
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/02/2017
ms.author: curtand
ms.reviewer: librown
ms.custom: end-user, seo-update-azuread-jan
ms.openlocfilehash: 51cfe2c448fde8c50f8b846979a4b35fa4be5ec1
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056024"
---
# <a name="sign-in-to-your-work-or-school-account-using-your-two-factor-verification-method"></a>Přihlaste se ke svému pracovnímu nebo školnímu účtu pomocí metody dvojúrovňové ověřování.

> [!NOTE]
> Účelem tohoto článku je projít si běžné prostředí pro přihlašování. Nápovědu k přihlašování nebo k řešení problémů najdete v článku o potížích [s Azure Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>K čemu se přihlašujete?
Vaše přihlašovací možnosti se liší v závislosti na tom, co se rozhodnete použít jako druhý faktor: telefonní hovor, ověřovací aplikace nebo texty. Vyberte možnost, která nejlépe popisuje, co děláte:

| Jak se přihlašujete? |
| --- |
| [S telefonním hovorem na mobilní telefon nebo na telefon do kanceláře](#signing-in-with-a-phone-call) |
| [S textem na mobilní telefon](#signing-in-with-a-text-message)
| [S oznámeními z aplikace Microsoft Authenticator](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| Pomocí ověřovacích kódů z aplikace Microsoft Authenticator |
| [Alternativním způsobem, protože nemůžu použít moji upřednostňovanou metodu hned teď](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Přihlášení pomocí telefonního hovoru
Následující informace popisují dvoustupňové ověřování při volání na mobilní telefon nebo na telefon do kanceláře.

1. Přihlaste se k aplikaci nebo službě, například Microsoft 365 pomocí uživatelského jména a hesla.  
2. Společnost Microsoft vás zavolá.  
3. Odpovězte na telefon a stiskněte klávesu #.  

## <a name="signing-in-with-a-text-message"></a>Přihlášení pomocí textové zprávy
Následující informace popisují dvoustupňové ověřování pomocí textové zprávy na mobilním telefonu:

1. Přihlaste se k aplikaci nebo službě, například Microsoft 365 pomocí uživatelského jména a hesla.
2. Společnost Microsoft vám pošle textovou zprávu, která obsahuje číselný kód.
3. Do pole přidaného na přihlašovací stránce zadejte kód.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Přihlášení pomocí aplikace Microsoft Authenticator
Následující informace popisují možnosti použití aplikace Microsoft Authenticator pro dvoustupňové ověřování. Existují dva různé způsoby použití aplikace. Můžete přijímat nabízená oznámení v zařízení, nebo můžete otevřít aplikaci a získat ověřovací kód.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Přihlášení pomocí oznámení z aplikace Microsoft Authenticator
1. Přihlaste se k aplikaci nebo službě, například Microsoft 365 pomocí uživatelského jména a hesla.
2. Microsoft pošle oznámení na Microsoft Authenticator aplikaci na vašem zařízení.

   ![Microsoft pošle oznámení](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Otevřete oznámení na telefonu a vyberte klíč pro **ověření** . Pokud vaše společnost vyžaduje kód PIN, zadejte ho sem.
4. Nyní byste měli být přihlášeni.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Přihlášení pomocí ověřovacího kódu v aplikaci Microsoft Authenticator

Pokud k získání ověřovacích kódů používáte aplikaci Microsoft Authenticator, zobrazí se při otevření aplikace číslo pod názvem vašeho účtu. Tento počet se mění každých 30 sekund, takže nebudete používat stejné číslo dvakrát. Po zobrazení výzvy k zadání ověřovacího kódu otevřete aplikaci a použijte libovolné číslo, které se aktuálně zobrazuje.

1. Přihlaste se k aplikaci nebo službě, například Microsoft 365 pomocí uživatelského jména a hesla.
2. Microsoft zobrazí výzvu k zadání ověřovacího kódu.

   ![Zadejte ověřovací kód.](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Otevřete aplikaci Microsoft Authenticator na telefonu a do pole, ke kterému se přihlašujete, zadejte kód.

## <a name="signing-in-with-an-alternate-method"></a>Přihlášení alternativním způsobem
Někdy nemáte telefon nebo zařízení, které jste nastavili jako upřednostňovanou metodu ověření. V této situaci doporučujeme, abyste pro svůj účet nastavili metody zálohování. V následující části se dozvíte, jak se přihlásit alternativním způsobem v případě, že vaše primární metoda nemusí být k dispozici.

1. Přihlaste se k aplikaci nebo službě, například Microsoft 365 pomocí uživatelského jména a hesla.
2. Vyberte **použít jinou možnost ověřování**. V závislosti na tom, kolik máte nastavení, uvidíte různé možnosti ověřování.
3. Vyberte alternativní metodu a přihlaste se.

   ![Použít alternativní metodu](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Další kroky
- Pokud máte problémy s přihlášením pomocí dvoustupňového ověřování, získáte další informace o potížích [s Azure Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

- Naučte se [Spravovat nastavení dvou kroků ověřování](multi-factor-authentication-end-user-manage-settings.md).

- Zjistěte, jak začít [s aplikací Microsoft Authenticator](user-help-auth-app-download-install.md) , abyste mohli používat oznámení místo textu a telefonních hovorů.
