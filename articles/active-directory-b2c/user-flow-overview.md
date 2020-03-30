---
title: Toky uživatelů ve službě Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Přečtěte si další informace o rozšiřitelném rámci zásad Služby Azure Active Directory B2C a o tom, jak vytvořit různé toky uživatelů.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c11bc48742c398d2048a236c7d00af044971f845
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185603"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Toky uživatelů ve službě Azure Active Directory B2C

Rozšiřitelné zásady rámce Azure Active Directory B2C (Azure AD B2C) je hlavní síla služby. Zásady plně popisují prostředí identity, jako je registrace, přihlášení nebo úpravy profilu. Portál Azure AD B2C obsahuje předdefinované konfigurovatelné zásady nazývané **toky uživatelů,** které vám pomohou nastavit nejběžnější úlohy identity.

## <a name="what-are-user-flows"></a>Co jsou toky uživatelů?

Tok uživatele umožňuje řídit chování v aplikacích konfigurací následujících nastavení:

- Typy účtů používané pro přihlášení, jako jsou sociální účty, jako je Facebook nebo místní účty
- Atributy, které mají být shromažďovány od spotřebitele, jako je jméno, PSČ a velikost obuvi
- Azure Multi-Factor Authentication
- Přizpůsobení uživatelského rozhraní
- Informace, které aplikace obdrží jako deklarace identity v tokenu

Můžete vytvořit mnoho uživatelských toků různých typů ve vašem tenantovi a použít je ve vašich aplikacích podle potřeby. Toky uživatelů lze znovu použít napříč aplikacemi. Tato flexibilita umožňuje definovat a upravovat prostředí identity s minimálními nebo žádné změny kódu. Aplikace aktivuje tok uživatele pomocí standardního požadavku na ověření HTTP, který obsahuje parametr toku uživatele. Jako odpověď je přijat vlastní [token.](tokens-overview.md)

Následující příklady ukazují parametr řetězce dotazu "p", který určuje tok uživatele, který má být použit:

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

Na webu Azure Portal se neustále přidávají nové [verze toků uživatelů.](user-flow-versions.md) Když začnete s Azure AD B2C, testované toky uživatelů se doporučuje používat. Když vytvoříte nový tok uživatele, zvolíte tok uživatele, který potřebujete, na kartě **Doporučené.**

V současné době se doporučují následující toky uživatelů:

- **Zaregistrujte se a přihlaste** se – zpracovává obě možnosti registrace a přihlášení s jedinou konfigurací. Uživatelé jsou vedeni správnou cestou v závislosti na kontextu. Doporučujeme použít tento tok uživatele přes tok **uživatele registrace** nebo **toku přihlášení** uživatele.
- **Úpravy profilu** – Umožňuje uživatelům upravovat informace o svém profilu.
- **Resetování hesla** – umožňuje nakonfigurovat, zda a jak mohou uživatelé resetovat své heslo.

## <a name="linking-user-flows"></a>Propojení uživatelských toků

**Sign-up nebo přihlášení** uživatele tok s místními účty obsahuje **zapomenuté heslo?** odkaz na první stránce prostředí. Kliknutím na tento odkaz automaticky nespustíte tok uživatelů pro obnovení hesla.

Místo toho je `AADB2C90118` kód chyby vrácen do vaší aplikace. Aplikace potřebuje zpracovat tento kód chyby spuštěním toku konkrétního uživatele, který resetuje heslo. Chcete-li zobrazit příklad, podívejte se na [jednoduchý ASP.NET ukázku,](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) která ukazuje propojení toků uživatelů.

## <a name="email-address-storage"></a>Úložiště e-mailových adres

E-mailová adresa může být vyžadována jako součást toku uživatele. Pokud se uživatel ověří u poskytovatele sociální identity, e-mailová adresa je uložena ve vlastnosti **otherMails.** Pokud je místní účet založen na uživatelském jménu, je e-mailová adresa uložena ve vlastnosti podrobností silného ověřování. Pokud je místní účet založen na e-mailové adrese, bude e-mailová adresa uložena ve vlastnosti **signInNames.**

E-mailová adresa není zaručeno, že bude ověřena v žádném z těchto případů. Správce klienta můžete zakázat ověření e-mailu v základních zásad pro místní účty. I když je ověření e-mailové adresy povoleno, adresy se neověřují, pokud pocházejí od poskytovatele sociální identity a nebyly změněny.

Pouze **ostatní Mails** a **signInNames** vlastnosti jsou vystaveny prostřednictvím rozhraní Microsoft Graph API. E-mailová adresa ve vlastnosti podrobnosti silného ověřování není k dispozici.

## <a name="next-steps"></a>Další kroky

Chcete-li vytvořit doporučené toky uživatelů, postupujte podle pokynů v [kurzu: Vytvoření toku uživatele](tutorial-create-user-flows.md).
