---
title: Kombinovaná registrace pro samoobslužné resetování HESLA Azure AD a vícefaktorové ověřování (preview) – Azure Active Directory
description: Azure Multi-Factor Authentication AD a hesla pomocí samoobslužné služby obnovit registraci (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 536d26abf563f18ed7cec6668fcd1d4223f5a135
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370155"
---
# <a name="combined-security-information-registration-preview"></a>Informace o registraci kombinované zabezpečení (preview)

Před kombinovaná registrace uživatelé registrovaní metody ověřování pro Azure Multi-Factor Authentication (MFA) a samoobslužné resetování hesla (SSPR) prostřednictvím dvou různých možností. Lidé byli zaměňovat, podobné metody byly použity pro Azure MFA a samoobslužné resetování HESLA, ale měli zaregistrovat pro jednotlivé funkce samostatně. Nyní s kombinovaná registrace uživatelé po registraci a získat výhody Azure MFA a samoobslužné resetování HESLA.

![Můj profil zobrazující registrované bezpečnostní údaje pro uživatele](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Než povolíte nové prostředí, projděte si tuto dokumentaci zaměřené na správce a dokumentaci zaměřené na uživatele, ujistěte se, že rozumíte funkce a dopad této funkce. Základní trénování v dokumentaci pro uživatele můžete připravit vaši uživatelé nové prostředí a pomáhají zajistit úspěšné zavedení.

|     |
| --- |
| Kombinované zabezpečení informace o registraci pro resetování hesla pomocí samoobslužné služby Azure Multi-Factor Authentication a Azure AD je funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

> [!IMPORTANT]
> Pokud je uživatel povolen pro původní verzi preview a vylepšené kombinovaná registrace prostředí, zobrazí se nové prostředí. Uživatelé, kteří jsou povoleny pro obě možnosti se zobrazí pouze nové prostředí Můj profil. Nové Můj profil v souladu s vzhledu a chování kombinovaná registrace a poskytuje bezproblémové prostředí pro uživatele. Uživatelé vidí můj profil tak, že přejdete do [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com).

Můj profil stránky jsou lokalizovány na základě aktuální nastavení jazyka na počítači přistoupit ke stránce. Společnost Microsoft ukládá jiným mezipaměť prohlížeče k vykreslení v poslední jazyk používaný bude pokračovat následné pokusy o přístup k nejnovější jazyk. Vymazání mezipaměti způsobí, že stránky, které znovu zpracovat. Pokud chcete vynutit konkrétní jazyk přidávání `?lng=de-DE` na konec adresy URL kde `de-DE` je nastavena na příslušný jazyk kódu vynutí stránky k vykreslení v daném jazyce.

![Nastavení samoobslužného resetování HESLA nebo jiné metody další bezpečnostní ověření](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-converged-registration"></a>Metody dostupné v sblížené registraci

V současné době podporuje kombinovaná registrace použít následující metody a akce pro tyto metody:

|   | Registrace | Změnit | Odstranění |
| --- | --- | --- | --- |
| Microsoft Authenticator | Ano (max. 5) | Ne | Ano |
| Jiné ověřovací aplikaci | Ano (max. 5) | Ne | Ano |
| Hardwarový token | Ne | Ne | Ano |
| Telefon | Ano | Ano | Ano |
| Alternativní telefon | Ano | Ano | Ano |
| Telefon do kanceláře | Ne | Ne | Ne |
| Email | Ano | Ano | Ano |
| Bezpečnostní otázky | Ano | Ne | Ano |
| Hesla aplikací | Ano | Ne | Ano |

> [!NOTE]
> Hesla aplikací jsou pouze dostupné pro uživatele, kteří bylo vynuceno pro vícefaktorové ověřování. Hesla aplikací nejsou k dispozici pro uživatele, kteří jsou povoleny pro vícefaktorové ověřování prostřednictvím zásad podmíněného přístupu.

Uživatelé můžou nastavit následující možnosti jejich výchozí metody pro vícefaktorové ověřování:

- Microsoft Authenticator – oznámení
- Aplikace Authenticator nebo hardware token – kód
- Telefonní hovor
- Textová zpráva

Jak Pokračujeme v do Azure AD přidat další metody ověřování takové, bude k dispozici v kombinované registraci těchto metod.

## <a name="combined-registration-modes"></a>Kombinovaná registrace režimy

Existují dva "režimy" kombinovaná registrace: přerušení a spravovat.

Režim přerušení, je to prostředí jako průvodce zobrazí uživateli při registraci nebo aktualizovat svoje bezpečnostní údaje při přihlášení.

Správa režimu je součástí profilu uživatele a umožňuje je spravovat svoje bezpečnostní údaje.

Pro oba režimy Pokud uživatel už zaregistroval metodu, která lze použít pro vícefaktorové ověřování, se potřebují k provedení MFA, než bude moct svoje bezpečnostní údaje.

### <a name="interrupt-mode"></a>Režim přerušení

Kombinovaná registrace dodržuje zásady vícefaktorového ověřování a samoobslužné resetování HESLA, pokud obě jsou povolené pro vašeho tenanta. Tyto zásady určují, zda dojde k přerušení uživatele k registraci při přihlašování v a které metody jsou k dispozici k registraci.

Následující seznam několik scénářů, kdy uživatel může zobrazit výzva k registraci nebo aktualizovat svoje bezpečnostní údaje:

* Registrace MFA vynucuje prostřednictvím služby Identity Protection: Uživatelé se výzva k registraci při přihlašování. Aby se zaregistrovali metody samoobslužné resetování HESLA a vícefaktorové ověřování (Pokud je uživatel povolen pro samoobslužné resetování HESLA).
* Registrace MFA vynutit prostřednictvím MFA na uživatele: Uživatelé se výzva k registraci při přihlašování. Aby se zaregistrovali metody samoobslužné resetování HESLA a vícefaktorové ověřování (Pokud je uživatel povolen pro samoobslužné resetování HESLA).
* Vynutit prostřednictvím podmíněného přístupu nebo jiné zásady registrace MFA: Uživatelé jsou požádáni o registraci při přístupu k prostředku, který vyžaduje vícefaktorové ověřování. Uživatelé budou registrovat metody samoobslužné resetování HESLA a vícefaktorové ověřování (Pokud je uživatel povolen pro samoobslužné resetování HESLA).
* Vynucené registrace samoobslužného resetování HESLA: Uživatelé jsou požádáni o registraci během svého přihlašování. Aby se zaregistrovali pouze metody samoobslužné resetování HESLA
* Vynutit aktualizaci samoobslužné resetování HESLA: Uživatelé musí zkontrolovat svoje bezpečnostní údaje v intervalu nastavil na správce. Uživatelé zobrazují své údaje a můžete vybrat "Vypadá v pořádku" nebo v případě potřeby proveďte změny.

Při registraci se nevynutí, uživatelům se zobrazí minimální počet metod, které jsou potřeba, aby vyhovovala zásadám vícefaktorové ověřování a samoobslužné resetování HESLA od těch s nejvíce alespoň zabezpečení.

Příklad:

* Uživatel je povolený pro samoobslužné resetování HESLA. Zásady SSPR vyžaduje dvě metody pro resetování a povolil kód mobilní aplikace, e-mail a telefon.
   * Tento uživatel je potřeba zaregistrovat dvěma způsoby.
      * Aplikace authenticator a telefon, se zobrazí ve výchozím nastavení.
      * Uživatele můžete zaregistrovat e-mailu místo aplikaci authenticator nebo v telefonu.

Následující diagram popisuje, jaké metody se zobrazí uživateli, když k přerušení registrace při přihlášení:

![Vývojový diagram kombinované zabezpečení informací](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Pokud máte vícefaktorové ověřování a samoobslužné resetování HESLA povoleno, doporučujeme vám vynucovat registrace MFA.

Pokud zásad samoobslužného resetování HESLA vyžaduje, aby uživatelé zkontrolovat svoje bezpečnostní údaje v pravidelných intervalech, uživatelé jsou při přihlášení k přerušení a zobrazí jejich registrované metody. Pokud je aktuální informace, které nebo, si mohou vybrat "Upravit informace o", mohou vybrat "Vypadá v pořádku" provádět změny.

### <a name="manage-mode"></a>Správa režimu

Uživatelé mají přístup ke správě tak, že přejdete do režimu [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo) nebo výběrem "Bezpečnostní údaje" Můj profil. Odtud uživatelů můžete přidat metody, odstranit nebo změnit stávající metody, změnit jejich výchozí metody a další.

## <a name="key-usage-scenarios"></a>Scénáře použití klíče

### <a name="set-up-security-info-during-sign-in"></a>Nastavte informace o zabezpečení při přihlášení

Registrace má vynucovat správcem.

Uživatel nenastavila všechny požadované bezpečnostní údaje a přejde na webu Azure portal. Po zadání uživatelského jména a hesla, bude uživatel vyzván k nastavení bezpečnostní údaje. Uživatel potom následuje kroky uvedené v průvodci k nastavení požadovaná bezpečnostní údaje. Uživatel může zvolit nastavení jiných metod než co se zobrazuje ve výchozím nastavení, pokud vaše nastavení povolit. Na konci Průvodce uživatele kontroly metody, které nastavené a jejich výchozí metody pro vícefaktorové ověřování. K dokončení procesu instalace, uživatel potvrdí informace a bude pokračovat na webu Azure portal.

### <a name="set-up-security-info-from-my-profile"></a>Nastavit informace o zabezpečení z Můj profil

Správce nebyl vynucené registrace.

Uživatel, který ještě nebyl nastavený všechny požadované bezpečnostní údaje přejde na [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com). Uživatel potom vybere **bezpečnostní údaje** v levém navigačním panelu. Odtud uživatel zvolí možnost přidání metody, vybere některou z metod, která je jim dostupná a postupuje podle pokynů k nastavení této metodě. Až budete hotovi, uživateli se zobrazí na metodu, kterou právě nastavené na stránce informace o zabezpečení.

### <a name="delete-security-info-from-my-profile"></a>Odstranit informace o zabezpečení z Můj profil

Uživatel, který má alespoň jednu metodu dříve nastavil přejde na [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). Uživatel zvolí možnost odstraňte jednu z metody dříve zaregistrovaný. Až budete hotovi, uživateli se už zobrazí tuto metodu na stránce informace o zabezpečení.

### <a name="change-default-method-from-my-profile"></a>Změnit výchozí metodu z Můj profil

Uživatel, který má alespoň jednu metodu, která lze použít pro MFA dříve nastavil přejde na [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). Uživatel změní jejich aktuální výchozí metoda na jiný výchozí metodu. Až budete hotovi, uživateli se zobrazí jejich nové výchozí metodu na stránce informace o zabezpečení.

## <a name="next-steps"></a>Další postup

[Povolení kombinovaná registrace ve vašem tenantovi](howto-registration-mfa-sspr-combined.md)

[Dostupné metody pro vícefaktorové ověřování a samoobslužné resetování HESLA](concept-authentication-methods.md)

[Konfigurace samoobslužného resetování hesla](howto-sspr-deployment.md)

[Konfigurovat ověřování Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
