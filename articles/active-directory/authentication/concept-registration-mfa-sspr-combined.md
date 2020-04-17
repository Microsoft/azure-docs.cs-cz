---
title: Kombinovaná registrace pro SSPR a MFA – Azure Active Directory
description: Azure AD Vícefaktorové ověřování a samoobslužné registrace hesla
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48350bf8f0ffb8681d95f6f42f9aa93256395f9a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534682"
---
# <a name="combined-security-information-registration-overview"></a>Přehled registrace kombinovaných bezpečnostních informací

Před kombinovanou registrací uživatelé zaregistrovali metody ověřování pro Azure Multi-Factor Authentication a samoobslužné resetování hesla (SSPR) samostatně. Lidé byli zmateni, že podobné metody byly použity pro multi-factor ověřování a SSPR, ale museli se zaregistrovat pro obě funkce. Nyní, s kombinovanou registrací, se uživatelé mohou zaregistrovat jednou a získat výhody multifaktorového ověřování a samodotykového ověřování.

Tento článek popisuje, co je kombinovaná registrace zabezpečení. Chcete-li začít s kombinovanou registrací zabezpečení, přečtěte si následující článek:

> [!div class="nextstepaction"]
> [Povolit kombinovanou registraci zabezpečení](howto-registration-mfa-sspr-combined.md)

![Můj profil zobrazující registrované bezpečnostní údaje pro uživatele](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Před povolením nového prostředí si přečtěte tuto dokumentaci zaměřenou na správce a dokumentaci zaměřenou na uživatele, abyste se ujistili, že rozumíte funkcím a účinkům této funkce. Založte školení na [uživatelské dokumentaci,](../user-help/user-help-security-info-overview.md) abyste připravili uživatele na nové prostředí a pomohli zajistit úspěšné zavedení.

Azure AD kombinovaná registrace informací o zabezpečení není momentálně k dispozici pro národní cloudy, jako je Azure US Government, Azure Germany nebo Azure China 21Vianet.

> [!IMPORTANT]
> Uživatelé, kteří jsou povoleny pro původní náhled a rozšířené kombinované registrace prostředí uvidí nové chování. Uživatelům, kteří jsou povoleni pro obě prostředí, se zobrazí pouze nové prostředí Můj profil. Nový můj profil je v souladu s vzhledem kombinované registrace a poskytuje uživatelům bezproblémový zážitek. Uživatelé mohou zobrazit můj [https://myprofile.microsoft.com](https://myprofile.microsoft.com)profil tak, že přejdou na .
>
> Při pokusu o přístup k možnosti Informace o zabezpečení se může vyskytnou chybová zpráva. Například "Omlouváme se, nemůžeme vás přihlásit". V takovém případě potvrďte, že nemáte žádný objekt zásad konfigurace nebo skupiny, který blokuje soubory cookie třetích stran ve webovém prohlížeči.

Stránky Můj profil jsou lokalizovány na základě nastavení jazyka počítače přistupujícího ke stránce. Společnost Microsoft ukládá nejnovější jazyk používaný v mezipaměti prohlížeče, takže následné pokusy o přístup ke stránkám se budou nadále vykreslovat v posledním použitém jazyce. Pokud vymažete mezipaměť, stránky se znovu vykreslí. Pokud chcete vynutit určitý jazyk, `?lng=<language>` můžete přidat na konec `<language>` adresy URL, kde je kód jazyka, který chcete vykreslit.

![Nastavení programu SSPR nebo jiných metod ověření zabezpečení](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Metody dostupné v kombinované registraci

Kombinovaná registrace podporuje následující metody a akce ověřování:

|   | Zaregistrovat | Změnit | Odstranit |
| --- | --- | --- | --- |
| Microsoft Authenticator | Ano (maximálně 5) | Ne | Ano |
| Další ověřovací aplikace | Ano (maximálně 5) | Ne | Ano |
| Hardwarový token | Ne | Ne | Ano |
| Telefon | Ano | Ano | Ano |
| Alternativní telefon | Ano | Ano | Ano |
| Telefon do kanceláře | Ne | Ne | Ne |
| E-mail | Ano | Ano | Ano |
| Bezpečnostní otázky | Ano | Ne | Ano |
| Hesla aplikací | Ano | Ne | Ano |
| Bezpečnostní klíče FIDO2<br />*Spravovaný režim pouze ze stránky [Informace o zabezpečení](https://mysignins.microsoft.com/security-info)*| Ano | Ano | Ano |

> [!NOTE]
> Hesla aplikací jsou k dispozici pouze uživatelům, kteří byli vynuceni pro vícefaktorové ověřování. Hesla aplikací nejsou k dispozici uživatelům, kteří jsou povoleni pro vícefaktorové ověřování prostřednictvím zásad podmíněného přístupu.

Uživatelé mohou nastavit jednu z následujících možností jako výchozí metodu vícefaktorového ověřování:

- Microsoft Authenticator – oznámení.
- Ověřovací aplikace nebo hardwarový token – kód.
- Telefonát.
- Textová zpráva.

Jak budeme i nadále přidávat další metody ověřování do Azure AD, tyto metody budou k dispozici v kombinované registraci.

## <a name="combined-registration-modes"></a>Kombinované režimy registrace

Existují dva režimy kombinované registrace: přerušení a správa.

- **Režim přerušení** je prostředí podobné průvodci, které se uživatelům zobrazí při registraci nebo aktualizaci bezpečnostních údajů při přihlášení.
- **Režim správy** je součástí uživatelského profilu a umožňuje uživatelům spravovat jejich bezpečnostní údaje.

Pro oba režimy uživatelé, kteří dříve zaregistrovali metodu, kterou lze použít pro vícefaktorové ověřování, budou muset před přístupem ke svým bezpečnostním údajům provést vícefaktorové ověřování.

### <a name="interrupt-mode"></a>Režim přerušení

Kombinovaná registrace respektuje zásady vícefaktorového ověřování i samodotykového ověřování, pokud jsou pro vašeho tenanta povoleny. Tyto zásady řídí, zda je uživatel přerušen pro registraci během přihlášení a jaké metody jsou k dispozici pro registraci.

Zde je několik scénářů, ve kterých mohou být uživatelé vyzváni k registraci nebo aktualizaci svých bezpečnostních údajů:

- Registrace vícefaktorového ověřování vynucená prostřednictvím ochrany identity: Uživatelé jsou požádáni o registraci během přihlášení. Registrují metody vícefaktorového ověřování a metody samodotykového ověřování (pokud je uživatel povolen pro samodotykové ověřování).
- Registrace vícefaktorového ověřování vynucená pomocí vícefaktorového ověřování pro jednotlivé uživatele: Uživatelé jsou požádáni o registraci během přihlášení. Registrují metody vícefaktorového ověřování a metody samodotykového ověřování (pokud je uživatel povolen pro samodotykové ověřování).
- Registrace vícefaktorového ověřování vynucená prostřednictvím podmíněného přístupu nebo jiných zásad: Uživatelé jsou požádáni o registraci při použití prostředku, který vyžaduje vícefaktorové ověřování. Registrují metody vícefaktorového ověřování a metody samodotykového ověřování (pokud je uživatel povolen pro samodotykové ověřování).
- Vynucená registrace samostředního veřejného zástupce: Uživatelé jsou požádáni o registraci během přihlášení. Registrují pouze metody sspr.
- Obnovení obnovení služby Při obnovení služby ASPR vynuceno: Uživatelé musí zkontrolovat své bezpečnostní údaje v intervalu nastaveném správcem. Uživatelům se zobrazí jejich informace a mohou potvrdit aktuální informace nebo v případě potřeby provést změny.

Při vynucení registrace se uživatelům zobrazí minimální počet metod, které jsou nutné pro kompatibilní s vícefaktorovým ověřováním a zásadami samoobslužného tisku, od většiny po nejméně zabezpečené.

Příklad:

- Uživatel je povolen pro sspr. Zásady sspr vyžadovaly dvě metody resetování a povolily kód mobilní aplikace, e-mail a telefon.
   - Tento uživatel je povinen zaregistrovat dvě metody.
      - Uživateli se ve výchozím nastavení zobrazí ověřovací aplikace a telefon.
      - Uživatel se může rozhodnout zaregistrovat e-mail namísto ověřovací aplikace nebo telefonu.

Tento vývojový diagram popisuje, které metody se uživateli zobrazí při přerušení registrace během přihlášení:

![Vývojový diagram kombinovaných bezpečnostních informací](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Pokud máte povoleno vícefaktorové ověřování i samodotykové ověřování, doporučujeme vynutit registraci vícefaktorového ověřování.

Pokud zásady samovlastního účtu vyžadují, aby uživatelé v pravidelných intervalech kontrolovali své bezpečnostní údaje, budou uživatelé během přihlašování přerušeni a zobrazí se všechny jejich registrované metody. Mohou potvrdit aktuální informace, pokud jsou aktuální, nebo mohou v případě potřeby provést změny. Uživatelé musí při přístupu na tuto stránku provádět vícefaktorové ověřování.

### <a name="manage-mode"></a>Správa režimu

Uživatelé mohou přistupovat [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) k režimu správy tak, že přejdou do možnosti **zabezpečení nebo** je vybere z mého profilu. Odtud mohou uživatelé přidávat metody, odstraňovat nebo měnit stávající metody, měnit výchozí metodu a další.

## <a name="key-usage-scenarios"></a>Scénáře použití klíčů

### <a name="set-up-security-info-during-sign-in"></a>Nastavení bezpečnostních údajů během přihlášení

Správce vynutil registraci.

Uživatel nenastavil všechny požadované bezpečnostní údaje a přejde na portál Azure. Po zadání uživatelského jména a hesla je uživatel vyzván k nastavení bezpečnostních údajů. Uživatel poté provede kroky uvedené v průvodci a nastaví požadované bezpečnostní údaje. Pokud to vaše nastavení umožňuje, může se uživatel rozhodnout nastavit jiné metody, než které jsou zobrazeny ve výchozím nastavení. Po dokončení průvodce uživatelé zkontrolují metody, které nastavují, a výchozí metodu vícefaktorového ověřování. K dokončení procesu instalace uživatel potvrdí informace a pokračuje na portál Azure.

### <a name="set-up-security-info-from-my-profile"></a>Nastavení bezpečnostních údajů z mého profilu

Správce nevynutil registraci.

Uživatel, který ještě nenastavil všechny požadované bezpečnostní [https://myprofile.microsoft.com](https://myprofile.microsoft.com)údaje, přejde na adresu . Uživatel vybere **informace zabezpečení** v levém podokně. Odtud se uživatel rozhodne přidat metodu, vybere některou z dostupných metod a provede kroky k nastavení této metody. Po dokončení se uživateli zobrazí metoda, která byla právě nastavena na stránce Informace o zabezpečení.

### <a name="delete-security-info-from-my-profile"></a>Odstranění bezpečnostních údajů z mého profilu

Uživatel, který dříve nastavil alespoň jednu [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)metodu, přejde na . Uživatel se rozhodne odstranit jednu z dříve registrovaných metod. Po dokončení uživatel již vidí tuto metodu na stránce Informace o zabezpečení.

### <a name="change-the-default-method-from-my-profile"></a>Změna výchozí metody ze složce Můj profil

Uživatel, který dříve nastavil alespoň jednu metodu, kterou lze použít [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)pro vícefaktorové ověřování, přejde na . Uživatel změní aktuální výchozí metodu na jinou výchozí metodu. Po dokončení se uživateli zobrazí nová výchozí metoda na stránce Informace o zabezpečení.

## <a name="next-steps"></a>Další kroky

Chcete-li začít, podívejte se do kurzů, které [umožňují samoobslužné resetování hesla](tutorial-enable-sspr.md) a [povolují azure multifaktorové ověřování](tutorial-enable-azure-mfa.md).

Zjistěte, jak [povolit kombinovanou registraci ve vašem tenantovi](howto-registration-mfa-sspr-combined.md) nebo [vynutit uživatelům znovu zaregistrovat metody ověřování](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Můžete také zkontrolovat [dostupné metody pro Azure Multi-Factor Authentication a SSPR](concept-authentication-methods.md).
