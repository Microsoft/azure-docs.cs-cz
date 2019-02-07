---
title: Azure MFA přihlášení s dvoustupňovým ověřováním | Dokumentace Microsoftu
description: Na této stránce zobrazí pokyny, ve kterém můžete přejít na najdete v různých přihlášení dostupné metody s Azure MFA.
keywords: ověřování uživatelů, přihlášení, přihlaste se pomocí mobilního telefonu, přihlaste se pomocí telefonní číslo do kanceláře
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: 13b82aa4f256bf0ec2d0805e85241d72e115d737
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55810197"
---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>Přihlašovací prostředí s ověřováním Azure Multi-Factor Authentication
> [!NOTE]
> Účelem tohoto článku je provede typické prostředí přihlásit. Pomoc s přihlášením nebo k řešení problémů najdete v tématu [potíže s Azure Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Co bude přihlašovacího prostředí?
Vaše přihlášení se liší v závislosti na tom, co si přejete použít jako druhý faktor: telefonní hovor, ověřování aplikace nebo textů. Zvolte možnost, která nejlépe popisuje, co právě děláte:

| Jak se vám přihlásit? |
| --- |
| [Pomocí telefonního hovoru na telefon Můj mobilní telefon nebo office](#signing-in-with-a-phone-call) |
| [S textem do mi na mobilní telefon](#signing-in-with-a-text-message)
| Oznámení z aplikace Microsoft Authenticator |
| Pomocí ověřovacích kódů z aplikace Microsoft Authenticator |
| [S alternativní metodu protože nelze použít upřednostňovanou metodu hned teď](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Přihlášení pomocí telefonního hovoru
Následující informace popisují dvoustupňové zkušenosti s ověřováním pomocí volání do telefonu mobilní telefon nebo office.

1. Přihlaste se k aplikaci nebo službu, jako je Office 365 pomocí uživatelského jména a hesla.  
2. Microsoft zavolá.  
3. Přijměte hovor a stiskem tlačítka #.  

## <a name="signing-in-with-a-text-message"></a>Přihlášení pomocí textové zprávy
Následující informace popisují prostředí dvoustupňové ověření pomocí textové zprávy na váš mobilní telefon:

1. Přihlaste se k aplikaci nebo službu, jako je Office 365 pomocí uživatelského jména a hesla.
2. Microsoft vám pošle textovou zprávu, která obsahuje číslo kódu.
3. V poli na stránce přihlášení zadejte kód.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Přihlášení pomocí aplikace Microsoft Authenticator
Následující informace popisují funkce pomocí aplikace Microsoft Authenticator pro dvoustupňové ověřování. Existují dva různé způsoby používání aplikace. Může přijímat nabízená oznámení na zařízení, nebo můžete otevřít aplikaci, abyste získali ověřovací kód.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Přihlásit se přes oznámení z aplikace Microsoft Authenticator
1. Přihlaste se k aplikaci nebo službu, jako je Office 365 pomocí uživatelského jména a hesla.
2. Microsoft pošle oznámení do aplikace Microsoft Authenticator na vašem zařízení.

  ![Microsoft pošle oznámení](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Otevřete oznámení na telefon a vyberte **ověřte** klíč. Pokud vaše společnost vyžaduje, PIN kód, zadejte ho sem.
4. By měla nyní být přihlásíte.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>K přihlášení pomocí ověřovací kód v aplikaci Microsoft Authenticator

Pokud používáte aplikaci Microsoft Authenticator získat ověřovací kódy, pak při otevření aplikace se zobrazí číslo pod názvem vašeho účtu. Toto číslo změní každých 30 sekund, takže nepoužívejte stejný počet dvakrát. Pokud se vám výzva k zadání ověřovacího kódu, otevřete aplikaci a pomocí libovolné číslo je právě otevřeno.

1. Přihlaste se k aplikaci nebo službu, jako je Office 365 pomocí uživatelského jména a hesla.
2. Microsoft vás vyzve k zadání ověřovacího kódu.

  ![Zadejte ověřovací kód.](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Otevřete aplikaci Microsoft Authenticator na vašem telefonu a zadejte kód do pole, ve kterém se přihlašujete.

## <a name="signing-in-with-an-alternate-method"></a>Přihlašování pomocí alternativního – metoda
Někdy není nutné telefonu nebo zařízení, které jste nastavili jako vaše upřednostňovaná metoda ověření. Tato situace je proto doporučujeme, abyste nastavili metody zálohování pro váš účet. Následující části se dozvíte, jak se přihlásit pomocí alternativní metodu, když primární způsob nemusí být k dispozici.

1. Přihlaste se k aplikaci nebo službu, jako je Office 365 pomocí uživatelského jména a hesla.
2. Vyberte **použít jinou možnost ověření**. Zobrazí možnosti různých ověřování založené na kolik máte nastavený.
3. Zvolte alternativní metodu a přihlaste se.

  ![Použití alternativní metody](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Další postup

Pokud máte potíže při přihlašování pomocí dvoustupňového ověření, získejte další informace na [potíže s Azure Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

Zjistěte, jak [spravovat nastavení dvoustupňového ověření](multi-factor-authentication-end-user-manage-settings.md).

Zjistěte, jak [Začínáme s aplikací Microsoft Authenticator](user-help-auth-app-download-install.md) tak, aby oznámení můžete použít k přihlášení, místo texty a telefonních hovorů.
