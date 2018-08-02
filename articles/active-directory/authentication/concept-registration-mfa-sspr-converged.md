---
title: Konvergované registrace pro samoobslužné resetování HESLA Azure AD a vícefaktorové ověřování
description: Registrace pro resetování ověřování službou Multi-Factor Authentication Azure AD a hesla pomocí samoobslužné služby
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: cdd100d113c3fbeda8ac840d479b065d648ac3ff
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414900"
---
# <a name="converged-registration-for-self-service-password-reset-and-azure-multi-factor-authentication"></a>Konvergované registrace pro samoobslužné resetování hesla a ověřování Azure Multi-Factor Authentication

Až doteď bylo muset zaregistrovat metody ověřování Azure Multi-Factor Authentication (MFA) a samoobslužné resetování hesla (SSPR) na dvou různých portálech uživatelů. Mnoho uživatelů byly matoucí skutečnost, že podobné metody byly použity pro Azure MFA a samoobslužné resetování HESLA a nebude registrace v obou portálů. Tato různorodost vedla k některým uživatelům, že nebudou moci používat Azure MFA nebo samoobslužné resetování HESLA v případě potřeby, což vede k volání technickou podporu a potenciálně nespokojený uživatele. Uživatelé teď může vytvářet jednou a získáte výhody Azure MFA a samoobslužné resetování HESLA, takže není třeba registrovat své metody ověřování pro tyto funkce dvakrát.  

Než povolíte toto nové prostředí pro vaši organizaci, doporučujeme, abyste si tento článek také naše [dokumentaci pro koncové uživatele](https://aka.ms/securityinfoguide) na vědomí následky prostředí budou mít u uživatelů. Můžete použít [dokumentaci pro koncové uživatele](https://aka.ms/securityinfoguide) trénování a připravit vaši uživatelé pro nové prostředí a zajistěte úspěšné zavedení.

|     |
| --- |
| Konvergované registrace pro samoobslužné resetování hesla a ověřování Azure Multi-Factor Authentication je funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Pokud chcete povolit uživatelům zaregistrovat metody ověřování pro Azure Multi-Factor Authentication a samoobslužné resetování hesla ve jednotné prostředí, proveďte následující kroky:

1. Přihlaste se k webu Azure portal jako globální správce nebo Správce uživatelů.
2. Přejděte do **Azure Active Directory**, **uživatelská nastavení**, **umožňuje spravovat nastavení přístupu funkce ve verzi preview panel**.
3. V části **uživatelé můžou používat funkce verze preview pro registraci a správu bezpečnostních údajů**, můžete také povolit pro **vybrané** skupiny uživatelů nebo pro **všechny** uživatelů.

Můžete teď moct uživatelé [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo) registrovat své metody ověřování pro vícefaktorové ověřování a samoobslužné resetování HESLA. Další informace o co uživatelé uvidí v této nové prostředí, najdete v našich [dokumentaci pro koncové uživatele](https://aka.ms/securityinfoguide).  

> [!NOTE]
> Po povolení tohoto prostředí, uživatelé, kteří registrace nebo potvrzení telefonního čísla nebo mobilních aplikací prostřednictvím nového prostředí budou mít možnost používat pro vícefaktorové ověřování a samoobslužné resetování HESLA, pokud tyto metody jsou povolené v zásadách vícefaktorového ověřování a samoobslužné resetování HESLA. Pokud zakážete pak toto prostředí, uživatelé, kteří přejít na předchozí stránku registrace samoobslužného resetování HESLA na aka.ms/ssprsetup muset provádět MFA mohli získat přístup ke stránce.  

## <a name="how-it-works"></a>Jak to funguje

Pokud uživatel už zaregistroval metody ověřování přes samostatné prostředí registrace vícefaktorového ověřování a samoobslužné resetování HESLA, není nutné tyto informace znovu zaregistrovat. Ale pokud se vaše nastavení vyžadovat od uživatelů registraci vícefaktorové ověřování a samoobslužné resetování HESLA, se může zobrazit výzva k zkontrolovat svoje bezpečnostní údaje při přihlášení.

Pokud uživatel zaregistroval metodu, která lze použít pro vícefaktorové ověřování, jsou vyzváni k provedení MFA, než bude moct nové prostředí.

Pokud vynucení registrace pro vícefaktorové ověřování a samoobslužné resetování HESLA a uživatel ještě nezaregistroval, jsou vyzváni k registraci při přihlášení.

Uživatelé, kteří se výzva k registraci při přihlášení se zobrazí následující možnosti:

![Konvergované registrace. Nastavit metody jako nový uživatel](./media/concept-registration-mfa-sspr-converged/concept-registration-add-methods.png)

> [!NOTE]
> Toto prostředí se zobrazí pouze pokud je uživatel vyzván k registraci při přihlašování. Uživatelé, kteří přejít přímo na nové prostředí na aka.ms/setupsecurityinfo uvidí odlišná verze prostředí, která je popsána dále v tomto článku.

Metody ověřování, zobrazí se budou měnit v závislosti na metody v zásad MFA nebo samoobslužné resetování HESLA povoleno. Uživatel bude požádán zaregistrovat minimální počet metod ověřování, které jsou potřebné k musí splňovat zásady vícefaktorového ověřování a zásad samoobslužného resetování HESLA. Pokud existuje určitá flexibilita, v jaké metody ověřování může uživatel zaregistrovat, můžete vybrat **zvolte bezpečnostní údaje** zvolit jiné metody ověřování.  

Na rozdíl od předchozích prostředí registrace MFA nebudou uživatelé vyzváni k registraci heslo aplikace. když přejdete do nového prostředí registrace. Místo toho, postupujte podle kroků uvedených v našem kurzu hesla aplikace pro registraci hesel aplikací v novém prostředí.  

Po dokončení registrace uživatel se nastaví automaticky jejich výchozí metoda MFA. Pokud uživatel zaregistrován ověřovací aplikaci, se do aplikace nastaví výchozí metodu. Pokud uživatel nezaregistroval ověřovací aplikace a registrovat jenom svoje telefonní číslo, se nastaví výchozí metodu na telefonní hovor. Uživatelé mohou změnit výchozí tak, že přejdete do [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo) a vyberete **změnit výchozí nastavení**.  

Pokud se registrace nevynucuje, uživatelé mohou spravovat své vlastní metody ověřování v [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo). Pokud uživatel už zaregistroval metodu, která slouží k provádění vícefaktorového ověřování, bude se mu zobrazit výzva k provedení MFA mohli získat přístup ke stránce.  

![Konvergované registrace. Upravit metody jako registrovaný uživatel](./media/concept-registration-mfa-sspr-converged/concept-registration-edit-methods.png)

Na této stránce uživatelům se zobrazí dříve zaregistrovaný ověřovací metody a metody ověřování, které jsou registrovány pro ně třeba telefonní číslo do kanceláře. Uživatelé mohou také přidat, upravit nebo odstranit své metody ověřování (s výjimkou telefonní číslo do kanceláře).  

Protokoly auditu pro toto nové prostředí existovat v rámci kategorie metod ověřování protokolu auditu.  

## <a name="known-issues"></a>Známé problémy

**Metoda MFA výchozí je nastavena na telefonní hovor, když se uživatel zaregistruje telefonu pomocí textové zprávy**
   * Někteří uživatelé si všimnout, že metoda MFA jejich výchozí je nastavena na telefonní hovor, po registraci jejich telefonní číslo uživatele pomocí textové zprávy. Uživatelé můžou tento problém vyřešit tak, že změníte jejich výchozí metoda podle těchto pokynů. 

**Nelze získat přístup k nové prostředí registrace po jejich výchozí metoda nezakáže správce uživatele**
   * Někteří uživatelé se možná nebudou mít přístup k nové prostředí registrace, pokud se dříve zaregistrovaní výchozí metoda MFA zakázal správce. Tady je příklad scénáře: 
      1. Uživatel dříve zaregistrovaný svého telefonního čísla a nastavte jejich výchozí metodu na telefonní hovor.
      2. Telefonní hovor jako metoda MFA nezakáže správce pro tenanta.
      3. Uživatel je vyzván k registraci při přihlašování, protože je nutné zaregistrovat další způsob, jak splnit tenanta zásad samoobslužného resetování HESLA.
      4. Uživatel pokusí zaregistrovat, ale nedaří se stránku, protože nemají výchozí metodu nastavit a zablokované ve smyčce.

## <a name="next-steps"></a>Další postup

[Zjistěte, jak nasadit Azure AD samoobslužné resetování hesla](howto-sspr-deployment.md)

[Zjistěte, jak vyžadovat vícefaktorové ověřování při přihlašování](howto-mfa-getstarted.md)

[Dokumentace konfigurace metoda ověřování koncového uživatele](https://aka.ms/securityinfoguide)