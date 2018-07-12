---
title: Předdefinované zásady v Azure Active Directory B2C | Dokumentace Microsoftu
description: Téma na rozhraní rozšiřitelných zásad Azure Active Directory B2C a o tom, jak vytvořit různé typy zásad.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5c89f39b2f94309ea3d99230f5265d834c7093d9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38477445"
---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Azure Active Directory B2C: Předdefinované zásady


Rozhraní rozšiřitelných zásad služby Azure Active Directory (Azure AD) B2C je základní sílu služby. Zásady například plně popisují činnosti identity uživatelů registrace, přihlašování a úpravy profilu. Například zásada registrace umožňuje řídit chování pomocí konfigurace následujícího nastavení:

* Typy účtů (účtů v sociálních sítích například Facebook) nebo místní účty, jako jsou e-mailové adresy, které uživatele můžete použít k registraci pro aplikaci
* Atributy (například křestní jméno, PSČ a velikost bot), které se mají shromažďovat od uživatele během registrace
* Použití služby Azure Multi-Factor Authentication
* Vzhled a chování registrace všech stránek
* Informace o (které manifesty jako deklarace identity v tokenu), aplikace obdrží, když zásady spouštění dokončí

Můžete vytvořit více zásad různých typů ve vašem tenantovi a podle potřeby využít ve svých aplikacích. Zásady můžete použít opakovaně napříč aplikacemi. Díky této flexibilitě vývojářům umožňuje definovat a upravovat činnosti identity uživatelů s minimálními nebo žádnými změnami na svůj kód.

Zásady se dají používat prostřednictvím jednoduchého pro vývojáře rozhraní. Vaše aplikace zásada se aktivuje pomocí standardní ověřování požadavek HTTP, který je (předání parametrů zásad v žádosti) a obdrží token přizpůsobené jako odpověď. Jediným rozdílem mezi požadavky, které vyvolají zásadu registrace a požadavky, které vyvolají zásad přihlašování je například název zásady, který se používá v parametru řetězce dotazu "p":

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

## <a name="create-a-sign-up-or-sign-in-policy"></a>Vytvoření zásady registrace nebo přihlašování

Tato zásada zpracovává i příjemce s konfigurací jedné prostředí registrace a přihlášení. Příjemci jsou vedla dolů správné cestě (registrace / přihlášení) v závislosti na kontextu. Také popisuje obsah tokenů, které bude aplikace přijímat po úspěšné registraci nebo přihlášení.  Ukázka kódu pro **registrace nebo přihlašování** zásady [tady k dispozici](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  Je doporučeno používat tyto zásady přes **registrace** zásad nebo **přihlášení** zásad.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-policy"></a>Vytvořit zásadu registrace

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-policy"></a>Vytvoření zásady přihlašování

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-policy"></a>Vytvoření zásady upravování profilu

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-policy"></a>Vytvoření zásady resetování hesla

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="preview-policies"></a>Zásady ve verzi Preview

Jak vydáváme nové funkce, v některých případech nemusí být k dispozici na existující zásady.  Plánujeme nahradit starší verze s nejnovějšími verzemi stejného typu, až tyto zásady zadejte obecné dostupnosti.  Existující zásady se nezmění a abyste mohli využívat tyto nové funkce budete muset vytvořit nové zásady.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>Jak připojím zásady registrace nebo přihlášení se zásady resetování hesla?
Při vytváření **registrace nebo přihlašování** zásady (s místní účty), uvidíte **zapomněli jste heslo?** odkaz na první stránce prostředí. Kliknutím na tento odkaz nebude automaticky aktivační událost heslo zásady pro resetování. 

Místo toho kód chyby: **`AADB2C90118`** se vrátí do vaší aplikace. Vaše aplikace potřebuje pro zpracování tohoto kódu vyvoláním zásady resetování hesla. Další informace najdete v tématu [ukázka, která předvádí postup propojení zásady](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>Mám použít zásady registrace nebo přihlášení nebo registraci zásady a zásady přihlašování?
Doporučujeme používat **registrace nebo přihlašování** zásad přes **registrace** zásad a **přihlášení** zásad.  

**Registrace nebo přihlášení** zásady k dispozici další možnosti, než **přihlášení** zásad. Také vám umožní použít přizpůsobení uživatelského rozhraní stránky a má lepší podporu pro lokalizaci. 

**Přihlášení** zásad se doporučuje, pokud už nebudete potřebovat k lokalizaci zásad, pouze potřebovat možnosti vedlejších přizpůsobení brandingu a chcete heslo integrovaný do jeho resetování.

## <a name="next-steps"></a>Další postup
* [Token, relace a konfigurace jednotného přihlašování](active-directory-b2c-token-session-sso.md)
* [Zakázání ověření e-mailu během registrace uživatelů](active-directory-b2c-reference-disable-ev.md)

