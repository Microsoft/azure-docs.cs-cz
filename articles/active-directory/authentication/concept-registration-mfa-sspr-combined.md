---
title: Kombinované registrace pro samoobslužné resetování HESLA Azure AD a Vícefaktorové ověřování (preview) – Azure Active Directory
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
ms.openlocfilehash: 4f3eec1f846f1b74ab3e19bca022d4e009540d1a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280005"
---
# <a name="combined-security-information-registration-preview"></a>Informace o registraci kombinované zabezpečení (preview)

Před kombinovaná registrace uživatelé registrovaní metody ověřování pro Azure Multi-Factor Authentication a samoobslužné resetování hesla (SSPR) samostatně. Uživatelé byli zaměňovat, podobné metody byly použity pro Vícefaktorové ověřování a samoobslužné resetování HESLA, ale měli zaregistrovat pro obě funkce. Nyní s kombinovaná registrace uživatelé po registraci a získat výhody Vícefaktorové ověřování a samoobslužné resetování HESLA.

![Můj profil zobrazující registrované bezpečnostní údaje pro uživatele](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Než povolíte nové prostředí, projděte si tuto dokumentaci zaměřené na správce a dokumentaci zaměřené na uživatele, ujistěte se, že rozumíte funkce a účinek této funkce. Základní trénování v dokumentaci pro uživatele můžete připravit vaši uživatelé nové prostředí a pomáhají zajistit úspěšné zavedení.

|     |
| --- |
| Kombinované zabezpečení registrační informace pro ověřování službou Multi-Factor Authentication a resetování hesla pomocí samoobslužné služby Azure Active Directory (Azure AD) je funkce ve verzi public preview služby Azure AD. Další informace o verzích Preview najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!IMPORTANT]
> Uživatelé, kteří jsou povoleny pro původní verzi preview a vylepšené kombinované registraci prostředí se zobrazí nové chování. Uživatelé, kteří jsou povoleny pro obě možnosti se zobrazí nové prostředí Můj profil. Nové Můj profil v souladu s vzhledu a chování kombinovaná registrace a poskytuje bezproblémové prostředí pro uživatele. Uživatelé vidí můj profil tak, že přejdete do [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com).

Moje stránky profilu jsou lokalizovány na základě nastavení jazyka počítače přistoupit ke stránce. Společnost Microsoft ukládá nejnovější jazyk používaný v mezipaměti prohlížeče k vykreslení v poslední jazyk používaný bude pokračovat následné pokusy o přístup ke stránce. Pokud jste vymazání mezipaměti, bude znovu zpracovat na stránkách. Pokud chcete vynutit konkrétní jazyk, můžete přidat `?lng=<language>` na konec adresy URL, kde `<language>` je kód jazyka, který chcete vykreslit.

![Nastavení samoobslužného resetování HESLA nebo jiné metody ověření zabezpečení](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Metody, které jsou k dispozici v kombinovaná registrace

Kombinované registrace podporuje následující metody ověřování a akce:

|   | Registrace | Změnit | Odstranění |
| --- | --- | --- | --- |
| Microsoft Authenticator | Ano (maximálně 5) | Ne | Ano |
| Jiné ověřovací aplikaci | Ano (maximálně 5) | Ne | Ano |
| Hardwarový token | Ne | Ne | Ano |
| Telefon | Ano | Ano | Ano |
| Alternativní telefon | Ano | Ano | Ano |
| Telefon do kanceláře | Ne | Ne | Ne |
| Email | Ano | Ano | Ano |
| Bezpečnostní otázky | Ano | Ne | Ano |
| Hesla aplikací | Ano | Ne | Ano |

> [!NOTE]
> Hesla aplikací jsou k dispozici pouze pro uživatele, kteří bylo vynuceno pro ověřování službou Multi-Factor Authentication. Hesla aplikací nejsou k dispozici pro uživatele, kteří jsou povoleny pro ověřování službou Multi-Factor Authentication prostřednictvím zásad podmíněného přístupu.

Uživatelé mohou nastavit jako výchozí metodu ověřování službou Multi-Factor Authentication jednu z následujících možností:

- Microsoft Authenticator – oznámení.
- Aplikace Authenticator nebo hardware token – kódu.
- Telefonní hovor.
- Textová zpráva.

Jak Pokračujeme v do Azure AD přidat další metody ověřování, bude k dispozici v kombinované registraci těchto metod.

## <a name="combined-registration-modes"></a>Kombinovaná registrace režimy

Existují dva režimy kombinovaná registrace: přerušení a spravovat.

- **Režim přerušení** je jako průvodce prostředí, budou zobrazovat uživatelům při registraci nebo aktualizovat svoje bezpečnostní údaje při přihlášení.

- **Správa režimu** je součástí uživatelský profil a umožňuje uživatelům spravovat svoje bezpečnostní údaje.

Pro oba režimy uživatelů, kteří zaregistrovali dříve metodu, která lze použít pro ověřování službou Multi-Factor Authentication muset provádět ověřování službou Multi-Factor Authentication, než bude moct svoje bezpečnostní údaje.

### <a name="interrupt-mode"></a>Režim přerušení

Kombinovaná registrace dodržuje zásady vícefaktorového ověřování a samoobslužné resetování HESLA, pokud obě jsou povolené pro vašeho tenanta. Tyto zásady řídí, jestli je uživatel přerušena pro registraci při přihlašování a které metody jsou k dispozici pro registraci.

Tady je několik scénářů, ve kterých mohou uživatelé vyzváni k registraci nebo aktualizovat svoje bezpečnostní údaje:

* Registrace pro ověřování službou Multi-Factor Authentication vynutit prostřednictvím služby Identity Protection: Uživatelé jsou požádáni o registraci při přihlašování. Aby se zaregistrovali metody samoobslužné resetování HESLA a Vícefaktorové ověřování (Pokud je uživatel povolen pro samoobslužné resetování HESLA).
* Registrace ověřování službou Multi-Factor Authentication vynutit prostřednictvím služby Multi-Factor Authentication na uživatele: Uživatelé jsou požádáni o registraci při přihlašování. Aby se zaregistrovali metody samoobslužné resetování HESLA a Vícefaktorové ověřování (Pokud je uživatel povolen pro samoobslužné resetování HESLA).
* Registrace pro ověřování službou Multi-Factor Authentication vynutit prostřednictvím podmíněného přístupu nebo jiné zásady: Uživatelé jsou požádáni o registraci při použití prostředek, který vyžaduje Vícefaktorové ověřování. Aby se zaregistrovali metody samoobslužné resetování HESLA a Vícefaktorové ověřování (Pokud je uživatel povolen pro samoobslužné resetování HESLA).
* Vynucené registrace samoobslužného resetování HESLA: Uživatelé jsou požádáni o registraci při přihlašování. Aby se zaregistrovali pouze metody samoobslužné resetování HESLA.
* Vynutit aktualizaci samoobslužné resetování HESLA: Uživatelé musí zkontrolovat svoje bezpečnostní údaje v intervalu nastavil na správce. Uživatelům se zobrazí jejich informace o a můžete potvrdit aktuální informace o nebo provést změny v případě potřeby.

Při registraci se nevynutí, uživatelům se zobrazí minimální počet metod, které jsou potřeba, aby vyhovovala zásadám Vícefaktorové ověřování a samoobslužné resetování HESLA, od těch s nejvíce alespoň zabezpečení.

Příklad:

* Uživatel je povolený pro samoobslužné resetování HESLA. Zásady SSPR vyžaduje dvě metody pro resetování a povolil kód mobilní aplikace, e-mail a telefon.
   * Tento uživatel je potřeba zaregistrovat dvěma způsoby.
      * Uživatel se ve výchozím nastavení zobrazí aplikace authenticator a telefon.
      * Uživatele můžete zaregistrovat e-mailu místo aplikaci authenticator nebo v telefonu.

Tato vývojový diagram popisuje, jaké metody se zobrazí uživateli, když k přerušení k registraci při přihlašování:

![Vývojový diagram kombinované zabezpečení informací](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Pokud máte Vícefaktorové ověřování a samoobslužné resetování HESLA povoleno, doporučujeme vám vynucovat registracích vícefaktorového ověřování.

Pokud zásad samoobslužného resetování HESLA vyžaduje, aby uživatelé zkontrolovat svoje bezpečnostní údaje v pravidelných intervalech, uživatelé jsou při přihlašování k přerušení a zobrazí jejich registrované metody. Pokud je aktuální, nebo pokud je nutné udělat změny mohou potvrdit aktuální informace.

### <a name="manage-mode"></a>Správa režimu

Uživatelé mají přístup ke správě režimu tak, že přejdete do [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo) nebo tak, že vyberete **bezpečnostní údaje** z Můj profil. Odtud uživatelů můžete přidat metody, odstranit nebo změnit stávající metody, změnit výchozí metodu a další.

## <a name="key-usage-scenarios"></a>Scénáře použití klíče

### <a name="set-up-security-info-during-sign-in"></a>Nastavte informace o zabezpečení při přihlašování

Registrace má vynucovat správcem.

Uživatel nenastavila všechny požadované bezpečnostní údaje a přejde na webu Azure portal. Po zadání uživatelského jména a hesla, bude uživatel vyzván k nastavení bezpečnostní údaje. Uživatel potom následuje kroky uvedené v průvodci k nastavení požadovaná bezpečnostní údaje. Pokud to umožňují vaše nastavení, můžete nastavit uživatele nastavit metody než ty, které zobrazí ve výchozím nastavení. Po dokončení Průvodce uživatelů kontrolovat metody, které nastavené a jejich výchozí metodu ověřování službou Multi-Factor Authentication. K dokončení procesu instalace, uživatel potvrdí informace a bude pokračovat na webu Azure portal.

### <a name="set-up-security-info-from-my-profile"></a>Nastavit informace o zabezpečení z Můj profil

Správce nebyl vynucené registrace.

Uživatel, který ještě nebyl nastavený všechny požadované bezpečnostní údaje přejde na [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com). Uživatel vybere **bezpečnostní údaje** v levém podokně. Odtud uživatel zvolí možnost přidání metody, vybere některou z metod, které jsou k dispozici a postupuje podle pokynů k nastavení této metodě. Až budete hotovi, uživateli se zobrazí metoda, kterou jste právě nastavili na stránce informace o zabezpečení.

### <a name="delete-security-info-from-my-profile"></a>Odstranit informace o zabezpečení z Můj profil

Uživatel, který má alespoň jednu metodu dříve nastavil přejde na [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). Uživatel zvolí možnost odstraňte jednu z metody dříve zaregistrovaný. Až budete hotovi, uživateli se už zobrazí tuto metodu na stránce informace o zabezpečení.

### <a name="change-the-default-method-from-my-profile"></a>Změnit výchozí metodu z Můj profil

Uživatel, který má alespoň jednu metodu, kterou můžete použít k ověření službou Multi-Factor Authentication dříve nastavil přejde na [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). Uživatel změní aktuální výchozí metoda na jiný výchozí metodu. Až budete hotovi, uživateli se zobrazí nový výchozí metodu na stránce informace o zabezpečení.

## <a name="next-steps"></a>Další postup

[Povolení kombinovaná registrace ve vašem tenantovi](howto-registration-mfa-sspr-combined.md)

[Dostupné metody pro Vícefaktorové ověřování a samoobslužné resetování HESLA](concept-authentication-methods.md)

[Konfigurace samoobslužného resetování hesla](howto-sspr-deployment.md)

[Konfigurace Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
