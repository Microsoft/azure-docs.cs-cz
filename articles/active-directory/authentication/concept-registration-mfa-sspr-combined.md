---
title: Kombinovaná registrace pro SSPR a MFA-Azure Active Directory
description: Služba Azure AD Multi-Factor Authentication a registrace samoobslužného resetování hesla (Preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c882d286a73900f58ef06e7c51b05c7237e39a7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425347"
---
# <a name="combined-security-information-registration-preview"></a>Registrace informací o kombinovaném zabezpečení (Preview)

Před kombinovanou registrací uživatelé zaregistrovali metody ověřování pro Azure Multi-Factor Authentication a Samoobslužné resetování hesla (SSPR) samostatně. Lidem se zaznamenalo, že podobné metody byly použity pro Multi-Factor Authentication a SSPR, ale musely se zaregistrovat pro obě funkce. Teď se při kombinované registraci můžou uživatelé zaregistrovat jednou a získat výhody Multi-Factor Authentication i SSPR.

![Můj profil zobrazující registrované bezpečnostní údaje pro uživatele](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Než začnete s novým prostředím, přečtěte si dokumentaci k tomuto správci a dokumentaci zaměřenou na uživatele, abyste se ujistili, že jsou funkce a účinky této funkce důležité. Založte školení na [dokumentaci pro uživatele](../user-help/user-help-security-info-overview.md) a připravte uživatele na nové prostředí a pomůžou zajistit úspěšné zavedení.

Registrace informací o kombinovaném zabezpečení Azure AD není aktuálně dostupná pro národní cloudy, jako je Azure USA, Azure Německo nebo Azure Čína 21Vianet.

|     |
| --- |
| Souhrnná registrace informací o zabezpečení pro Multi-Factor Authentication a Azure Active Directory (Azure AD) Samoobslužné resetování hesla je funkce Public Preview služby Azure AD. Další informace o verzích Preview najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!IMPORTANT]
> Pro uživatele, kteří jsou povoleni pro původní verzi Preview i pro rozšířené kombinované registrační prostředí, se zobrazí nové chování. Uživatelům, kteří jsou u obou funkcí povoleni, se zobrazí pouze nové prostředí s profilem. Nový můj profil se zarovnává s vzhledem a chováním kombinované registrace a poskytuje uživatelům bezproblémové prostředí. Uživatelé uvidí svůj profil tak, že budou mít na [https://myprofile.microsoft.com](https://myprofile.microsoft.com).

> [!NOTE] 
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
| E-mail | Ano | Ano | Ano |
| Bezpečnostní otázky | Ano | Ne | Ano |
| Hesla aplikací | Ano | Ne | Ano |

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

V obou režimech budou uživatelé, kteří dříve zaregistrovali metodu, kterou lze použít pro Multi-Factor Authentication, vyžadovat Multi-Factor Authentication předtím, než budou mít přístup k jejich bezpečnostním údajům.

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

Uživatelé mají přístup ke správě režimu tak, že na [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) nebo vyberou **bezpečnostní údaje** z mého profilu. Odtud mohou uživatelé přidávat metody, odstraňovat nebo měnit existující metody, měnit výchozí metodu a další.

## <a name="key-usage-scenarios"></a>Scénáře použití klíčů

### <a name="set-up-security-info-during-sign-in"></a>Nastavení bezpečnostních údajů během přihlášení

Správce vynutil registraci.

Uživatel nenainstaloval všechny požadované bezpečnostní údaje a přejde na Azure Portal. Po zadání uživatelského jména a hesla se uživateli zobrazí výzva k nastavení bezpečnostních údajů. Uživatel pak podle kroků uvedených v průvodci nastaví požadované bezpečnostní údaje. Pokud to nastavení povoluje, může uživatel zvolit možnost nastavit jiné metody než ty, které jsou ve výchozím nastavení zobrazené. Po dokončení průvodce si uživatelé Prohlédněte metody, které nastavili, a jejich výchozí metodu pro Multi-Factor Authentication. K dokončení procesu instalace uživatel potvrdí informace a pokračuje Azure Portal.

### <a name="set-up-security-info-from-my-profile"></a>Nastavení bezpečnostních údajů z mého profilu

Správce vynutil registraci.

Uživatel, který ještě nevytvořil všechny požadované bezpečnostní údaje, přejde na [https://myprofile.microsoft.com](https://myprofile.microsoft.com). Uživatel vybere v levém podokně **informace o zabezpečení** . Odtud se uživatel rozhodne přidat metodu, vybere některou z dostupných metod a postupuje podle pokynů k nastavení této metody. Po dokončení uživatel uvidí metodu, která se právě nastavila na stránce informace o zabezpečení.

### <a name="delete-security-info-from-my-profile"></a>Odstranit informace o zabezpečení z mého profilu

Uživatel, který dříve nainstaloval alespoň jednu metodu, přejde na [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Uživatel se rozhodne odstranit jednu z dříve registrovaných metod. Po dokončení již uživatel na stránce informace o zabezpečení nevidí tuto metodu.

### <a name="change-the-default-method-from-my-profile"></a>Změna výchozí metody z mého profilu

Uživatel, který dříve nainstaloval alespoň jednu metodu, kterou lze použít pro Multi-Factor Authentication přejít na [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Uživatel změní aktuální výchozí metodu na jinou výchozí metodu. Po dokončení uživatel uvidí novou výchozí metodu na stránce bezpečnostní údaje.

## <a name="next-steps"></a>Další kroky

[Vynutí uživatele, aby znovu zaregistrovali metody ověřování.](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[Povolit kombinovanou registraci ve vašem tenantovi](howto-registration-mfa-sspr-combined.md)

[Vytváření sestav využití SSPR a MFA a přehledy](howto-authentication-methods-usage-insights.md)

[Dostupné metody pro Multi-Factor Authentication a SSPR](concept-authentication-methods.md)

[Konfigurace samoobslužného resetování hesla](howto-sspr-deployment.md)

[Konfigurace Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
