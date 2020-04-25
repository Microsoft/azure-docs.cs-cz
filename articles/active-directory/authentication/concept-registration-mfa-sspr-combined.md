---
title: Kombinovaná registrace pro SSPR a MFA – Azure Active Directory
description: Služba Azure AD Multi-Factor Authentication a registrace samoobslužného resetování hesla
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
ms.openlocfilehash: 4d4caaf8704f2ee49f8f094ad22065ae462154be
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143926"
---
# <a name="combined-security-information-registration-overview"></a>Přehled souhrnné registrace informací o zabezpečení

Před kombinovanou registrací uživatelé zaregistrovali metody ověřování pro Azure Multi-Factor Authentication a Samoobslužné resetování hesla (SSPR) samostatně. Lidem se zaznamenalo, že podobné metody byly použity pro Multi-Factor Authentication a SSPR, ale musely se zaregistrovat pro obě funkce. Teď se při kombinované registraci můžou uživatelé zaregistrovat jednou a získat výhody Multi-Factor Authentication i SSPR.

Tento článek popisuje, jaká kombinovaná registrace zabezpečení je. Informace o tom, jak začít používat kombinovanou registraci zabezpečení, najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Povolit kombinovanou registraci zabezpečení](howto-registration-mfa-sspr-combined.md)

![Můj profil zobrazující registrované bezpečnostní údaje pro uživatele](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Než začnete s novým prostředím, přečtěte si dokumentaci k tomuto správci a dokumentaci zaměřenou na uživatele, abyste se ujistili, že jsou funkce a účinky této funkce důležité. Založte školení na [dokumentaci pro uživatele](../user-help/user-help-security-info-overview.md) a připravte uživatele na nové prostředí a pomůžou zajistit úspěšné zavedení.

Registrace informací o kombinovaném zabezpečení Azure AD není aktuálně dostupná pro národní cloudy, jako je Azure USA, Azure Německo nebo Azure Čína 21Vianet.

> [!IMPORTANT]
> Pro uživatele, kteří jsou povoleni pro původní verzi Preview i pro rozšířené kombinované registrační prostředí, se zobrazí nové chování. Uživatelům, kteří jsou u obou funkcí povoleni, se zobrazí pouze nové prostředí s profilem. Nový můj profil se zarovnává s vzhledem a chováním kombinované registrace a poskytuje uživatelům bezproblémové prostředí. Uživatelé můžou profil zobrazit kliknutím na [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
>
> Při pokusu o přístup k možnosti informace o zabezpečení se může zobrazit chybová zpráva. Například "Bohužel vás nemůžeme přihlásit". V takovém případě potvrďte, že nemáte žádné konfigurační objekty nebo objekt zásad skupiny blokující soubory cookie třetích stran ve webovém prohlížeči.

Stránky s profilem jsou lokalizovány na základě nastavení jazyka počítače, který přistupuje k stránce. Microsoft ukládá nejnovější jazyk používaný v mezipaměti prohlížeče, takže následné pokusy o přístup ke stránkám se budou dál zobrazovat v posledním použitém jazyce. Pokud mezipaměť vymažete, stránky se znovu vykreslí. Pokud chcete vynutit určitý jazyk, můžete přidat `?lng=<language>` na konec adresy URL, kde `<language>` je kód jazyka, který chcete vykreslit.

![Nastavení SSPR nebo jiných metod ověřování zabezpečení](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Metody dostupné v kombinované registraci

Kombinovaná registrace podporuje následující metody ověřování a akce:

|   | Zaregistrovat | Změnit | Odstranit |
| --- | --- | --- | --- |
| Microsoft Authenticator | Ano (maximálně 5) | Ne | Ano |
| Jiná ověřovací aplikace | Ano (maximálně 5) | Ne | Ano |
| Hardwarový token | Ne | Ne | Ano |
| Telefon | Ano | Ano | Ano |
| Alternativní telefon | Ano | Ano | Ano |
| Telefon do kanceláře | Ne | Ne | Ne |
| E-mailu | Ano | Ano | Ano |
| Bezpečnostní otázky | Ano | Ne | Ano |
| Hesla aplikací | Ano | Ne | Ano |
| FIDO2 klíče zabezpečení<br />*Spravovaný režim pouze ze stránky [bezpečnostní údaje](https://mysignins.microsoft.com/security-info)*| Ano | Ano | Ano |

> [!NOTE]
> Hesla aplikací jsou k dispozici pouze pro uživatele, kteří byli vynutili Multi-Factor Authentication. Hesla aplikací nejsou k dispozici uživatelům, kteří jsou povoleni pro Multi-Factor Authentication prostřednictvím zásad podmíněného přístupu.

Uživatelé můžou jako výchozí metodu Multi-Factor Authentication nastavit jednu z následujících možností:

- Microsoft Authenticator – oznámení
- Ověřovací aplikace nebo hardwarový token – kód.
- Telefonní hovor.
- Textová zpráva.

Jak budeme dál přidávat do Azure AD další metody ověřování, budou tyto metody k dispozici v kombinované registraci.

## <a name="combined-registration-modes"></a>Režimy kombinované registrace

Existují dva režimy kombinované registrace: přerušení a správa.

- **Režim přerušení** je prostředí podobné průvodci, které se uživatelům prezentuje při registraci nebo aktualizaci bezpečnostních údajů při přihlášení.
- **Režim správy** je součástí profilu uživatele a umožňuje uživatelům spravovat své bezpečnostní údaje.

V obou režimech budou uživatelé, kteří dříve zaregistrovali metodu, kterou lze použít pro Multi-Factor Authentication, vyžadovat Multi-Factor Authentication předtím, než budou mít přístup k jejich bezpečnostním údajům. Uživatelé musí před pokračováním v používání dříve registrovaných metod potvrdit jejich údaje. 

### <a name="interrupt-mode"></a>Režim přerušení

Kombinovaná registrace respektuje zásady Multi-Factor Authentication a SSPR, pokud jsou obě pro vašeho tenanta povolené. Tyto zásady určují, jestli má uživatel během přihlašování přerušit registraci, a jaké metody jsou k dispozici pro registraci.

Tady je několik scénářů, ve kterých můžou být uživatelé vyzváni, aby zaregistrovali nebo aktualizovali své bezpečnostní údaje:

- Multi-Factor Authentication registrace vynutila prostřednictvím Identity Protection: uživatelům se během přihlašování zobrazí výzva k registraci. Registrují Multi-Factor Authentication metody a metody SSPR (Pokud je uživatel povolený pro SSPR).
- Multi-Factor Authentication registrace vynutila prostřednictvím Multi-Factor Authentication pro jednotlivé uživatele: během přihlašování se zobrazí výzva k registraci uživatelů. Registrují Multi-Factor Authentication metody a metody SSPR (Pokud je uživatel povolený pro SSPR).
- Multi-Factor Authentication registrace vynutila prostřednictvím podmíněného přístupu nebo jiných zásad: uživatelům se zobrazí výzva k registraci při použití prostředku, který vyžaduje Multi-Factor Authentication. Registrují Multi-Factor Authentication metody a metody SSPR (Pokud je uživatel povolený pro SSPR).
- SSPR registrace byla vynutila: uživatelům se během přihlašování zobrazí výzva k registraci. Registrují pouze metody SSPR.
- Vynutila se aktualizace SSPR: uživatelům se vyžaduje, aby zkontrolovali své bezpečnostní údaje v intervalu nastaveném správcem. Uživatelům se zobrazí jejich informace a může potvrdit aktuální informace nebo provést změny v případě potřeby.

Když se registrace vynutila, zobrazí se uživatelům minimální počet metod, které musí být v souladu se zásadami Multi-Factor Authentication a SSPR, od nejvíce po nejméně bezpečnou.

Příklad:

- Uživatel je povolený pro SSPR. Zásada SSPR vyžadovala dvě metody resetování a povolení kódu mobilní aplikace, e-mailu a telefonu.
   - Tento uživatel je nutný k registraci dvou metod.
      - Ve výchozím nastavení se uživateli zobrazí ověřovací aplikace a telefon.
      - Uživatel se může rozhodnout zaregistrovat e-mail místo aplikace ověřovatele nebo telefonu.

Tento vývojový diagram popisuje, které metody se zobrazí uživateli při přerušení registrace během přihlašování:

![Diagram kombinovaných bezpečnostních údajů](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Pokud máte povolené Multi-Factor Authentication i SSPR, doporučujeme, abyste vynutili registraci Multi-Factor Authentication.

Pokud zásady SSPR vyžadují, aby si uživatelé zkontrolovali své bezpečnostní údaje v pravidelných intervalech, přeruší se během přihlašování a zobrazí se všechny registrované metody. V případě, že je to aktuální, můžou potvrdit aktuální informace, nebo můžou dělat změny, pokud je potřebují. Uživatelé musí při přístupu k této stránce provádět službu Multi-Factor Authentication.

### <a name="manage-mode"></a>Režim správy

Uživatelé mají přístup ke správě režimu tak, [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) že v něm nebo vyberou **bezpečnostní údaje** z mého profilu. Odtud mohou uživatelé přidávat metody, odstraňovat nebo měnit existující metody, měnit výchozí metodu a další.

## <a name="key-usage-scenarios"></a>Scénáře použití klíčů

### <a name="set-up-security-info-during-sign-in"></a>Nastavení bezpečnostních údajů během přihlášení

Správce vynutil registraci.

Uživatel nenainstaloval všechny požadované bezpečnostní údaje a přejde na Azure Portal. Po zadání uživatelského jména a hesla se uživateli zobrazí výzva k nastavení bezpečnostních údajů. Uživatel pak podle kroků uvedených v průvodci nastaví požadované bezpečnostní údaje. Pokud to nastavení povoluje, může uživatel zvolit možnost nastavit jiné metody než ty, které jsou ve výchozím nastavení zobrazené. Po dokončení průvodce si uživatelé Prohlédněte metody, které nastavili, a jejich výchozí metodu pro Multi-Factor Authentication. K dokončení procesu instalace uživatel potvrdí informace a pokračuje Azure Portal.

### <a name="set-up-security-info-from-my-profile"></a>Nastavení bezpečnostních údajů z mého profilu

Správce vynutil registraci.

Uživatel, který ještě nevytvořil všechny požadované bezpečnostní údaje, se dostane [https://myprofile.microsoft.com](https://myprofile.microsoft.com)do. Uživatel vybere v levém podokně **informace o zabezpečení** . Odtud se uživatel rozhodne přidat metodu, vybere některou z dostupných metod a postupuje podle pokynů k nastavení této metody. Po dokončení uživatel uvidí metodu, která se právě nastavila na stránce informace o zabezpečení.

### <a name="delete-security-info-from-my-profile"></a>Odstranit informace o zabezpečení z mého profilu

Uživatel, který dříve nainstaloval alespoň jednu metodu, přejde na [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Uživatel se rozhodne odstranit jednu z dříve registrovaných metod. Po dokončení již uživatel na stránce informace o zabezpečení nevidí tuto metodu.

### <a name="change-the-default-method-from-my-profile"></a>Změna výchozí metody z mého profilu

Uživatel, který dříve nainstaloval alespoň jednu metodu, kterou lze použít pro Multi-Factor Authentication přejít na [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Uživatel změní aktuální výchozí metodu na jinou výchozí metodu. Po dokončení uživatel uvidí novou výchozí metodu na stránce bezpečnostní údaje.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít, přečtěte si kurzy, jak [Povolit Samoobslužné resetování hesla](tutorial-enable-sspr.md) a [Povolit Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

Naučte se, jak [Povolit kombinovanou registraci ve vašem tenantovi](howto-registration-mfa-sspr-combined.md) nebo [přinutit uživatele, aby znovu zaregistrovali metody ověřování](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Můžete si také projít [dostupné metody pro Azure Multi-Factor Authentication a SSPR](concept-authentication-methods.md).
