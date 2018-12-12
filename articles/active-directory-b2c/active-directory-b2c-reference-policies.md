---
title: Toky uživatelů v Azure Active Directory B2C | Dokumentace Microsoftu
description: Téma na rozhraní rozšiřitelných zásad Azure Active Directory B2C a o tom, jak vytvořit různých typech toků uživatele.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d4a93b04b8ad86a6a6d36a5bdaf3209b49e7a9dc
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877078"
---
# <a name="azure-active-directory-b2c-user-flows"></a>Azure Active Directory B2C: Toky uživatelů


Rozhraní rozšiřitelných zásad služby Azure Active Directory (Azure AD) B2C je základní sílu služby. Zásady například plně popisují činnosti identity uživatelů registrace, přihlašování a úpravy profilu. Můžete nastavit zvládnout běžné úkoly identity, na portálu Azure AD B2C zahrnuje předdefinované, Konfigurovatelné zásady volá **toky uživatelů**. Například tok registrace uživatele umožňuje řídit chování konfigurací následujícího nastavení:

* Typy účtů (účtů v sociálních sítích například Facebook) nebo místní účty, jako jsou e-mailové adresy, které uživatele můžete použít k registraci pro aplikaci
* Atributy (například křestní jméno, PSČ a velikost bot), které se mají shromažďovat od uživatele během registrace
* Použití služby Azure Multi-Factor Authentication
* Vzhled a chování registrace všech stránek
* Informace o (které manifesty jako deklarace identity v tokenu), aplikace obdrží, když spuštění dokončí toku uživatele

Můžete vytvořit několik toků uživatelů z různých typů ve vašem tenantovi a podle potřeby využít ve svých aplikacích. Toky uživatelů můžete použít opakovaně napříč aplikacemi. Díky této flexibilitě vývojářům umožňuje definovat a upravovat činnosti identity uživatelů s minimálními nebo žádnými změnami na svůj kód.

Toky uživatelů se dají používat prostřednictvím jednoduchého pro vývojáře rozhraní. Vaše aplikace spustí tok uživatele s použitím standardní ověřování požadavek HTTP, který je (předání parametrů toku uživatele v požadavku) a obdrží token přizpůsobené jako odpověď. Například jediným rozdílem mezi požadavky, které vyvolají tok registrace uživatele a požadavky, které vyvolají tok přihlášení uživatele se uživatelské jméno tok, který se používá v parametru řetězce dotazu "p":

```

https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow

```

```

https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow

```

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Vytvořit tok registrace / přihlášení uživatele

Tento tok uživatele zpracuje obě příjemce s konfigurací jedné prostředí registrace a přihlášení. Příjemci jsou vedla dolů správné cestě (registrace / přihlášení) v závislosti na kontextu. Také popisuje obsah tokenů, které bude aplikace přijímat po úspěšné registraci nebo přihlášení.  Ukázka kódu pro **registrace nebo přihlašování** tok uživatele je [tady k dispozici](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  Je doporučeno používat tento tok uživatele přes **registrace** tok uživatele nebo **přihlášení** tok uživatele.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-user-flow"></a>Vytvoření toku pro zápis uživatele

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-user-flow"></a>Vytvořit tok přihlášení uživatele

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-user-flow"></a>Vytvořit tok uživatele upravování profilu

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-user-flow"></a>Vytvořit tok uživatele resetování hesla

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="preview-user-flows"></a>Toky uživatelů pro verzi Preview

Jako vydáváme nové funkce, v některých případech nemusí být k dispozici na existující zásady nebo toky uživatelů.  Plánujeme nahradit starší verze s nejnovějšími verzemi stejného typu, až tyto toky uživatelů zadejte obecné dostupnosti.  Existující zásady nebo toky uživatelů se nezmění a abyste mohli využívat tyto nové funkce budete muset vytvořit nové toky uživatelů.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-do-i-link-a-sign-up-or-sign-in-user-flow-with-a-password-reset-user-flow"></a>Jak připojím tok registrace / přihlášení uživatele s tokem uživatele resetování hesla?
Při vytváření **registrace nebo přihlašování** tok uživatele (s místní účty), uvidíte **zapomněli jste heslo?** odkaz na první stránce prostředí. Kliknutím na tento odkaz nebude automaticky aktivační událost heslo resetovat tok uživatele. 

Místo toho kód chyby: **`AADB2C90118`** se vrátí do vaší aplikace. Vaše aplikace potřebuje pro zpracování tohoto kódu vyvoláním tok uživatele resetování hesla. Další informace najdete v tématu [ukázka, která předvádí postup propojení toky uživatelů](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-user-flow-or-a-sign-up-user-flow-and-a-sign-in-user-flow"></a>Mám použít tok registrace / přihlášení uživatele nebo tok registrace uživatele a tok přihlášení uživatele?
Doporučujeme používat **registrace nebo přihlašování** tok uživatele přes **registrace** tok uživatele a **přihlášení** tok uživatele.  

**Registrace nebo přihlašování** tok uživatele k dispozici další možnosti, než **přihlášení** tok uživatele. Také vám umožní použít přizpůsobení uživatelského rozhraní stránky a má lepší podporu pro lokalizaci. 

**Přihlášení** toku uživatele se doporučuje, pokud nepotřebujete k lokalizaci vaše toky uživatelů, pouze potřebovat možnosti vedlejších přizpůsobení brandingu a chcete heslo integrovaný do jeho resetování.

## <a name="next-steps"></a>Další postup
* [Token, relace a konfigurace jednotného přihlašování](active-directory-b2c-token-session-sso.md)
* [Zakázání ověření e-mailu během registrace uživatelů](active-directory-b2c-reference-disable-ev.md)

