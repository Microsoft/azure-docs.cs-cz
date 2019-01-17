---
title: Toky uživatelů v Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o rozhraní rozšiřitelných zásad Azure Active Directory B2C a jak vytvořit různé toky uživatelů.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bcbd26c8e78e29daa78a7e50f2f49b095103f696
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54351777"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Toky uživatelů v Azure Active Directory B2C

Rozhraní rozšiřitelných zásad služby Azure Active Directory (Azure AD) B2C je základní sílu služby. Zásady plně popisují činnosti, jako registrace, přihlašování a úpravy profilu. Můžete nastavit zvládnout běžné úkoly identity, na portálu Azure AD B2C zahrnuje předdefinované, Konfigurovatelné zásady volá **toky uživatelů**. 

## <a name="what-are-user-flows"></a>Co jsou toky uživatelů?

Tok uživatele umožňuje řídit chování ve svých aplikacích pomocí konfigurace následujících nastavení:

- Účet používaný typy pro přihlašování, jako je například účtů na sociálních sítích, jako je Facebook nebo místní účty
- Atributů shromážděných od uživatele, například křestní jméno, PSČ, a velikost bot
- Azure Multi-Factor Authentication
- Přizpůsobení uživatelského rozhraní
- Informace, které aplikace obdrží jako deklarace identity v tokenu 

Můžete vytvořit mnoho toky uživatelů z různých typů ve vašem tenantovi a podle potřeby využít ve svých aplikacích. Toky uživatelů můžete použít opakovaně napříč aplikacemi. Díky této flexibilitě umožňuje definovat a upravovat činnosti s minimálními nebo žádnými změnami kódu. Vaše aplikace spustí tok uživatele pomocí standardní ověřovací požadavek HTTP, která zahrnuje parametr tok uživatele. Přizpůsobené [token](active-directory-b2c-reference-tokens.md) bude přijata jako odpověď. 

Parametr řetězce dotazu "p", který určuje tok uživatele, který se má použít v následujících příkladech:

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

Na webu Azure Portal, nový [verzích toky uživatelů](user-flow-versions.md) se neustále přidávají. Když začnete s Azure AD B2C, testovat uživatele, kterého toky se doporučují pro použití. Když vytvoříte nový tok uživatele, vyberte tok uživatele, který potřebujete, na základě **doporučená** kartu.

Aktuálně se doporučují tyto toky uživatelů:

- **Registrace a přihlášení** – zpracovává i prostředí registrace a přihlášení s konfigurací jedné. Uživatelé jsou vedla dolů správné cestě, v závislosti na kontextu. Je doporučeno používat tento tok uživatele přes **registrace** tok uživatele nebo **přihlášení** tok uživatele.
- **Upravování profilu** – umožňuje uživatelům upravovat informace profilu.
- **Resetování hesla** – umožňuje nakonfigurovat, jestli a jak uživatelé si můžou resetovat svoje heslo.

## <a name="linking-user-flows"></a>Toky uživatelů pro propojení

A **registrace nebo přihlašování** zahrnuje tok uživatele s místním účtům **zapomněli jste heslo?** odkaz na první stránce prostředí. Kliknutím na tento odkaz nebude automaticky aktivační událost heslo resetovat tok uživatele. 

Místo toho kód chyby: `AADB2C90118` se vrátí do vaší aplikace. Vaše aplikace potřebuje ke zpracování tomto kódu chyby spuštěním tok konkrétního uživatele, který může resetovat hesla. Pokud chcete zobrazit příklad, podívejte se na [jednoduchého ukázkového ASP.NET](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) , který ukazuje propojení toky uživatelů.

## <a name="email-address-storage"></a>E-mailovou adresu úložiště

E-mailová adresa může být vyžadován jako součást toku uživatele. Pokud uživatel se ověřuje pomocí zprostředkovatele sociální identity, e-mailová adresa je uložen v **otherMails** vlastnost. Pokud místní účet je založen na uživatelské jméno, e-mailovou adresu uložená ve vlastnosti podrobností silné ověřování. Pokud místní účet podle e-mailovou adresu, e-mailová adresa není uložen v **signInNames** vlastnost.
 
E-mailová adresa není zaručeno, že se dá ověřit v každém z těchto případů. Správce tenanta můžete zakázat ověření e-mailu v základní zásady pro místní účty. I když je povoleno ověření e-mailové adresy, adresy neověří. Pokud pocházejí ze zprostředkovatele sociální identity a nebyly změněny.
 
Pouze **otherMails** a **signInNames** vlastnosti jsou vystaveny prostřednictvím Active Directory Graph API. E-mailovou adresu ve vlastnosti podrobností silné ověřování není k dispozici.

## <a name="next-steps"></a>Další postup

Můžete vytvářet toky doporučené uživatele, postupujte podle pokynů v [kurzu: Vytvořit tok uživatele](tutorial-create-tenant.md).


