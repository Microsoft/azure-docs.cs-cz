---
title: Konvergované registrace pro samoobslužné resetování HESLA Azure AD a vícefaktorové ověřování (public preview)
description: Azure Multi-Factor Authentication AD a hesla pomocí samoobslužné služby obnovit registraci (verze public preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: fd56d8e0500b80bcce743a8865b6ca90ca8658a6
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55080544"
---
# <a name="converged-registration-for-self-service-password-reset-and-azure-multi-factor-authentication-public-preview"></a>Konvergované registrace pro samoobslužné resetování hesla a ověřování Azure Multi-Factor Authentication (public preview)

Až doteď bylo muset zaregistrovat metody ověřování Azure Multi-Factor Authentication (MFA) a samoobslužné resetování hesla (SSPR) na dvou různých portálech uživatelů. Mnoho uživatelů byly matoucí, podobné metody byly použity pro Azure MFA a samoobslužné resetování HESLA a nebude registrace v obou portálů. Někteří uživatelé nemohli pak použít Azure MFA nebo samoobslužné resetování HESLA při potřebné, vedoucí k volání na helpdesk. 

Uživatelé teď může vytvářet jednou a získáte výhody Azure MFA a samoobslužné resetování HESLA. Uživatelé nemusí registrovat své metody ověřování pro tyto funkce dvakrát.  

Než povolíte toto nové prostředí pro vaši organizaci, doporučujeme, abyste si v tomto článku a [dokumentaci pro uživatele](https://aka.ms/securityinfoguide) porozumět dopadu, který prostředí budou mít u uživatelů. Dokumentace pro uživatele můžete použít k trénování a připravit vaši uživatelé pro nové prostředí a pomáhají zajistit úspěšné zavedení.

|     |
| --- |
| Konvergované registrace pro samoobslužné resetování hesla a ověřování Azure Multi-Factor Authentication je funkce ve verzi public preview služby Azure Active Directory (Azure AD). Další informace o verzích Preview najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Pokud chcete povolit uživatelům zaregistrovat metody ověřování pro Azure Multi-Factor Authentication a samoobslužné resetování hesla ve jednotné prostředí, proveďte následující kroky:

1. Přihlaste se k webu Azure portal jako globální správce nebo Správce uživatelů.
2. Přejděte do **Azure Active Directory** > **uživatelská nastavení** > **umožňuje spravovat nastavení přístupu funkce ve verzi preview panel**.
3. V části **uživatelé můžou používat funkce verze preview pro registraci a správu bezpečnostních údajů**, můžete také povolit pro **vybrané** skupiny uživatelů nebo pro **všechny** uživatelů.

Můžete teď moct uživatelé [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo) registrovat své metody ověřování pro vícefaktorové ověřování a samoobslužné resetování HESLA. Další informace o co uživatelé uvidí v této nové prostředí, najdete v článku [dokumentaci pro uživatele](https://aka.ms/securityinfoguide).  

> [!NOTE]
> Po povolení této možnosti se uživatelé, kteří registrace nebo potvrzení jejich telefonní číslo nebo mobilních aplikací prostřednictvím nového prostředí můžete používat pro vícefaktorové ověřování a samoobslužné resetování HESLA, pokud tyto metody jsou povolené v zásadách vícefaktorového ověřování a samoobslužné resetování HESLA. Pokud zakážete pak toto prostředí, uživatelé, kteří přejít na předchozí stránku registrace samoobslužného resetování HESLA na https:/aka.ms/ssprsetup muset provádět ověřování službou Multi-Factor Authentication před přístupem na stránce.  

## <a name="how-it-works"></a>Jak to funguje

Pokud uživatel už zaregistroval metody ověřování přes samostatné prostředí registrace vícefaktorového ověřování a samoobslužné resetování HESLA, nepotřebují tyto informace znovu zaregistrovat. Ale pokud se vaše nastavení vyžadovat od uživatelů registraci vícefaktorové ověřování a samoobslužné resetování HESLA, může se zobrazit výzva ke kontrole jejich informace o zabezpečení při přihlášení.

Pokud uživatel zaregistroval metodu, která lze použít pro vícefaktorové ověřování, zobrazí se výzva k provedení ověřování Multi-Factor Authentication před přístupem k nové prostředí.

Pokud vynucení registrace pro vícefaktorové ověřování a samoobslužné resetování HESLA a uživatel ještě nezaregistroval, zobrazí se výzva k registraci při přihlášení.

Uživatelé, kteří se výzva k registraci při přihlášení najdete v následující možnosti:

![Konvergované registrace. Nastavte metody jako nový uživatel.](./media/concept-registration-mfa-sspr-converged/concept-registration-add-methods.png)

> [!NOTE]
> Toto prostředí se zobrazí, jenom když je uživatel vyzván k registraci při přihlašování. Uživatelé, kteří přejít přímo na prostředí za https://aka.ms/setupsecurityinfo najdete v článku na jinou verzi prostředí, která je popsána dále v tomto článku.

Změna metody zobrazené ověřování na základě metod v zásad vícefaktorového ověřování a samoobslužné resetování HESLA povoleno. Uživateli se zobrazí výzva k registraci minimální počet metod ověřování, které jsou potřebné k musí splňovat zásady vícefaktorového ověřování a zásad samoobslužného resetování HESLA. Pokud existuje určitá flexibilita, v jaké metody ověřování může uživatel zaregistrovat, můžete vybrat **zvolte bezpečnostní údaje** zvolit jiné metody ověřování.  

> [!NOTE]
> Pokud povolíte použití oznámení mobilní aplikace a kód mobilní aplikace, uživatelé, kteří zaregistrovat aplikaci Microsoft Authenticator, prostřednictvím oznámení můžete použít oznámení a kód ověřit svoji identitu.

Na rozdíl od předchozích prostředí registrace MFA uživatelé vyzváni k registraci heslo aplikace prostřednictvím nového prostředí registrace. Místo toho že by měl postupujte podle kroků uvedených v tomto kurzu hesla aplikace pro registraci hesel aplikací v novém prostředí.  

Po dokončení registrace uživatel je automaticky nastaven jejich výchozí metoda MFA. Pokud uživatel zaregistrován ověřovací aplikaci, výchozí metoda je nastavena na aplikaci. Pokud uživatel nezaregistroval ověřovací aplikace a registrovat jenom svoje telefonní číslo, výchozí metoda je nastavena na telefonní hovor. Uživatelé mohou změnit výchozí tak, že přejdete do https://aka.ms/setupsecurityinfo a vyberete **změnit výchozí nastavení**.  

Pokud se registrace nevynucuje, uživatelé mohou spravovat své vlastní metody ověřování v https://aka.ms/setupsecurityinfo. Pokud uživatel už zaregistroval metodu, která lze použít pro vícefaktorové ověřování, zobrazí se výzva k provedení ověřování Multi-Factor Authentication před přístupem na stránce.  

![Konvergované registrace. Upravte metody jako registrovaní uživatelé.](./media/concept-registration-mfa-sspr-converged/concept-registration-edit-methods.png)

Na této stránce uživatelé uvidí dříve zaregistrovaný ověřovací metody a metody ověřování, které jsou registrovány pro ně, jako je například telefonní číslo do kanceláře. Uživatelé mohou také přidat, upravit nebo odstranit své metody ověřování (s výjimkou telefonní číslo do kanceláře).  

Protokoly auditu pro toto nové prostředí existovat v rámci kategorie metod ověřování protokolu auditu.  

## <a name="known-issues"></a>Známé problémy

Zjistili jsme chybu s sblížené registrací, kde nejsou B2B uživatele typu Host, kteří jsou povoleny pro konvergované registrace moci zaregistrovat pro vícefaktorové ověřování. Když jsou směrované na stránce pro registraci chyb stránky navýšení kapacity. Naši vývojáři jsou si tohoto problému vědomi a pracují na poskytování řešení. Prozatím doporučujeme vytvořit skupinu a vyloučit všechny uživatele B2B z této skupiny.

**Metoda MFA výchozí je nastavena na telefonní hovor, když se uživatel zaregistruje telefonu pomocí textové zprávy**

   * Někteří uživatelé si všimnout, že jejich metoda MFA výchozí je nastavena na telefonní hovor, až se jejich telefonní číslo registraci pomocí textové zprávy. Uživatelé tento problém můžete vyřešit tak, že změníte jejich výchozí metoda podle pokynů v článku [spravovat svoje bezpečnostní údaje (preview)](../user-help/security-info-manage-settings.md#change-your-info).

**Uživatel nemá přístup k nové prostředí registrace po jejich výchozí metoda nezakáže správce**

   * Někteří uživatelé nebudou moct přístup k nové prostředí registrace, pokud jim správce zakázal metodu MFA dřív registrovaná výchozí hodnota. Tady je příklad scénáře:
      1. Uživatel dříve zaregistrovaný svého telefonního čísla a nastavte jejich výchozí metodu na telefonní hovor.
      2. Telefonní hovor jako metoda MFA nezakáže správce pro tenanta.
      3. Uživatel je vyzván k registraci při přihlašování, protože je nutné zaregistrovat další způsob, jak splnit tenanta zásad samoobslužného resetování HESLA.
      4. Uživatel pokusí zaregistrovat, ale nemůže přistupovat k stránky a je zablokované ve smyčce, protože není nastavena výchozí metodu.

## <a name="next-steps"></a>Další postup

[Zjistěte, jak nasadit Azure AD samoobslužné resetování hesla](howto-sspr-deployment.md)

[Zjistěte, jak vyžadovat vícefaktorové ověřování pro přihlášení](howto-mfa-getstarted.md)

[Další informace o konfiguraci metody ověřování uživatele](https://aka.ms/securityinfoguide)
