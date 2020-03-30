---
title: Přihlášení pomocí ověřování pomocí pracovního nebo školního účtu – Azure AD
description: Přečtěte si, jak se přihlásit k pracovnímu nebo školnímu účtu pomocí různých dvoufaktorových metod ověřování.
services: active-directory
author: curtand
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: curtand
ms.reviewer: librown
ms.custom: end-user, seo-update-azuread-jan
ms.openlocfilehash: 33cf9e284d2206ea497af7a5da7c3cf4a890cc87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064083"
---
# <a name="sign-in-to-your-work-or-school-account-using-your-two-factor-verification-method"></a>Přihlaste se ke svému pracovnímu nebo školnímu účtu pomocí dvoufaktorové metody ověření

> [!NOTE]
> Účelem tohoto článku je projít typické přihlašovací prostředí. Nápovědu k přihlášení nebo řešení problémů najdete v tématu [Potíže s vícefaktorovým ověřováním Azure](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Jaký bude váš zážitek z přihlášení?
Vaše přihlašovací prostředí se liší v závislosti na tom, co se rozhodnete použít jako druhý faktor: telefonní hovor, ověřovací aplikaci nebo textové zprávy. Vyberte možnost, která nejlépe vystichne, co právě děláte:

| Jak se přihlásíte? |
| --- |
| [Při telefonním hovoru na mobilní telefon nebo do kanceláře](#signing-in-with-a-phone-call) |
| [S textovou zprávou na můj mobilní telefon](#signing-in-with-a-text-message)
| [S oznámeními z aplikace Microsoft Authenticator](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| S ověřovacími kódy z aplikace Microsoft Authenticator |
| [S alternativní metodou, protože nemohu použít svou preferovanou metodu právě teď](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Přihlášení pomocí telefonního hovoru
Následující informace popisují dvoustupňové ověření pomocí volání na mobilní telefon nebo telefon do kanceláře.

1. Přihlaste se k aplikaci nebo službě, jako je Office 365, pomocí uživatelského jména a hesla.  
2. Společnost Microsoft vám volá.  
3. Zvedněte telefon a stiskněte tlačítko # .  

## <a name="signing-in-with-a-text-message"></a>Přihlášení pomocí textové zprávy
Následující informace popisují dvoustupňové ověření pomocí textové zprávy do mobilního telefonu:

1. Přihlaste se k aplikaci nebo službě, jako je Office 365, pomocí uživatelského jména a hesla.
2. Společnost Microsoft vám odešle textovou zprávu, která obsahuje číselný kód.
3. Do pole uvedeného na přihlašovací stránce zadejte kód.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Přihlášení pomocí aplikace Microsoft Authenticator
Následující informace popisují zkušenosti s používáním aplikace Microsoft Authenticator pro dvoustupňová ověření. Existují dva různé způsoby, jak používat aplikaci. V zařízení můžete dostávat nabízená oznámení nebo můžete otevřít aplikaci a získat ověřovací kód.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Přihlášení pomocí oznámení z aplikace Microsoft Authenticator
1. Přihlaste se k aplikaci nebo službě, jako je Office 365, pomocí uživatelského jména a hesla.
2. Společnost Microsoft odešle oznámení do aplikace Microsoft Authenticator na vašem zařízení.

   ![Společnost Microsoft odesílá oznámení](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Otevřete oznámení v telefonu a vyberte klíč **Ověřit.** Pokud vaše společnost vyžaduje KÓD PIN, zadejte jej sem.
4. Nyní byste měli být přihlášeni.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Přihlášení pomocí ověřovacího kódu pomocí aplikace Microsoft Authenticator

Pokud k získání ověřovacích kódů používáte aplikaci Microsoft Authenticator, pak při otevření aplikace se pod názvem účtu zobrazí číslo. Toto číslo se mění každých 30 sekund, takže nepoužijete stejné číslo dvakrát. Když budete požádáni o ověřovací kód, otevřete aplikaci a použijte libovolné číslo, které se právě zobrazuje.

1. Přihlaste se k aplikaci nebo službě, jako je Office 365, pomocí uživatelského jména a hesla.
2. Společnost Microsoft vás vyzve k zadání ověřovacího kódu.

   ![Zadejte ověřovací kód.](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Otevřete v telefonu aplikaci Microsoft Authenticator a zadejte kód do pole, kde se přihlašujete.

## <a name="signing-in-with-an-alternate-method"></a>Přihlášení pomocí alternativní metody
Někdy nemáte telefon nebo zařízení, které jste nastavili jako upřednostňovanou metodu ověření. Tato situace je důvod, proč doporučujeme nastavit metody zálohování pro váš účet. V následující části se zobrazí způsob přihlášení pomocí alternativní metody, pokud primární metoda nemusí být k dispozici.

1. Přihlaste se k aplikaci nebo službě, jako je Office 365, pomocí uživatelského jména a hesla.
2. Vyberte **Použít jinou možnost ověření**. Zobrazí se různé možnosti ověření podle toho, kolik máte nastavení.
3. Zvolte alternativní metodu a přihlaste se.

   ![Použít alternativní metodu](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Další kroky
- Pokud máte problémy s přihlášením pomocí dvoustupňového ověření, získejte další informace na [webu Potíže s vícefaktorovým ověřováním Azure](multi-factor-authentication-end-user-troubleshoot.md).

- Přečtěte si, jak [spravovat nastavení dvoustupňového ověření](multi-factor-authentication-end-user-manage-settings.md).

- Přečtěte si, jak [začít s aplikací Microsoft Authenticator,](user-help-auth-app-download-install.md) abyste se místo textových zpráv a telefonních hovorů mohli přihlásit pomocí oznámení.
