---
title: Toky uživatelů v Azure Active Directory B2C | Microsoft Docs
description: Přečtěte si další informace o rozšiřitelném rozhraní zásad Azure Active Directory B2C a o tom, jak vytvářet různé toky uživatelů.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fdc7d9fce3ebce4603369262f164ba72b1e39499
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484311"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Toky uživatelů v Azure Active Directory B2C

Rozšiřitelná architektura zásad Azure Active Directory B2C (Azure AD B2C) je základní silo služby. Zásady plně popisují prostředí identity, jako je registrace, přihlašování nebo úpravy profilu. Aby vám pomohly nastavit nejběžnější úkoly identity, Azure AD B2C portál obsahuje předdefinované a konfigurovatelné zásady nazývané **uživatelské toky**.

## <a name="what-are-user-flows"></a>Co jsou toky uživatelů?

Tok uživatele umožňuje řídit chování ve vašich aplikacích konfigurací následujících nastavení:

- Typy účtů používané pro přihlašování, jako jsou účty sociálních sítí, jako je například Facebook nebo místní účet
- Atributy, které se mají shromáždit od příjemce, jako je křestní jméno, PSČ a velikost bot
- Azure Multi-Factor Authentication
- Přizpůsobení uživatelského rozhraní
- Informace, které aplikace obdrží jako deklarace identity v tokenu

V tenantovi můžete vytvořit mnoho uživatelských toků různých typů a podle potřeby je používat ve svých aplikacích. Toky uživatelů se dají znovu použít napříč aplikacemi. Tato flexibilita umožňuje definovat a upravovat prostředí identity s minimálními nebo žádnými změnami kódu. Vaše aplikace spustí tok uživatele pomocí standardní žádosti o ověření protokolu HTTP, která obsahuje parametr toku uživatele. Přizpůsobený [token](tokens-overview.md) se přijímá jako odpověď.

Následující příklady znázorňují parametr řetězce dotazu "p", který určuje tok uživatele, který se má použít:

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

## <a name="user-flow-versions"></a>Verze toku uživatele

V Azure Portal se neustále přidávají nové [verze uživatelských toků](user-flow-versions.md) . Až začnete s Azure AD B2C, doporučuje se testovat toky uživatelů, které můžete použít. Při vytváření nového toku uživatele si z **Doporučené** karty zvolíte tok uživatele, který potřebujete.

V současné době jsou doporučeny následující toky uživatelů:

- **Zaregistrujte se a přihlaste se a přihlaste** se pomocí jediné konfigurace. Uživatelé mají v závislosti na kontextu správnou cestu. Doporučuje se používat tento tok uživatelů v uživatelském toku pro **registraci** nebo v uživatelském toku **přihlášení** .
- **Úpravy profilu** – umožňuje uživatelům upravovat informace o profilu.
- **Resetování hesla** – umožňuje nakonfigurovat, jestli a jak můžou uživatelé resetovat heslo.

## <a name="linking-user-flows"></a>Propojení toků uživatelů

Tok uživatelů **registrace nebo přihlašování** pomocí místních účtů zahrnuje **zapomenuté heslo** a odkaz na první stránce prostředí. Kliknutím na tento odkaz se automaticky neaktivuje tok uživatele resetování hesla.

Místo toho se do vaší aplikace vrátí kód chyby `AADB2C90118`. Vaše aplikace potřebuje zpracovat tento kód chyby spuštěním konkrétního toku uživatele, který resetuje heslo. Příklad zobrazíte tak, že se podíváte na [jednoduchý vzorek ASP.NET](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) , který ukazuje propojení uživatelských toků.

## <a name="email-address-storage"></a>Úložiště e-mailových adres

E-mailová adresa může být vyžadována jako součást toku uživatele. Pokud se uživatel ověří pomocí zprostředkovatele sociální identity, e-mailová adresa se uloží do vlastnosti **otherMails** . Pokud je místní účet založen na uživatelském jménu, bude e-mailová adresa uložena ve vlastnosti detail silného ověřování. Pokud je místní účet založen na e-mailové adrese, uloží se tato e-mailová adresa do vlastnosti **signInNames** .

Tato e-mailová adresa není v žádném z těchto případů ověřena. Správce klienta může zakázat ověřování e-mailů v základních zásadách pro místní účty. I když je povolené ověřování e-mailových adres, adresy se neověřují, pokud pocházejí od poskytovatele sociální identity a nezměnily se.

Prostřednictvím rozhraní Microsoft Graph API se zveřejňují jenom vlastnosti **otherMails** a **signInNames** . E-mailová adresa v vlastnosti detail silného ověřování není k dispozici.

## <a name="next-steps"></a>Další kroky

Pokud chcete vytvořit toky doporučeného uživatele, postupujte podle pokynů v tématu [kurz: vytvoření toku uživatele](tutorial-create-user-flows.md).
