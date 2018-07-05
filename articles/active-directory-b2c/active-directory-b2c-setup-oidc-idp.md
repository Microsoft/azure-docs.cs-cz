---
title: Přidání poskytovatele OpenID Connect identity v integrovaných zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Průvodce přehled o tom, jak přidat poskytovatele OpenID Connect v předdefinované zásady v rámci Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e5baf95cd2426c9753620cba7c5a67b4c1672788
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444221"
---
# <a name="azure-active-directory-b2c-add-a-custom-openid-connect-identity-provider-in-built-in-policies"></a>Azure Active Directory B2C: Přidání vlastního zprostředkovatele identity OpenID Connect v předdefinované zásady

>[!NOTE]
> Tato funkce je ve verzi public preview. Nepoužívejte tuto funkci v produkčním prostředí.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) je ověřovací protokol, postavený na OAuth 2.0, který umožňuje bezpečně přihlásit uživatele. Většina poskytovatelů identit, které používají tento protokol vyžadoval, jako například [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md), jsou podporovány v Azure AD B2C. Tento článek vysvětluje, jak můžete přidat vlastní poskytovatele OpenID Connect identity do integrovaných zásad.

## <a name="configuring-a-custom-openid-connect-identity-provider"></a>Konfigurace vlastního zprostředkovatele identity OpenID Connect

Chcete-li přidat vlastního zprostředkovatele identity OpenID Connect:

1. Postupujte podle těchto kroků [přechod k nastavením Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na webu Azure Portal.
1. Klikněte na **zprostředkovatelé Identity**.
1. Klikněte na **Přidat**.
1. Pro **typ zprostředkovatele identit**vyberte **OpenID Connect**.

### <a name="setting-up-the-openid-connect-identity-provider"></a>Nastavení identity poskytovatele OpenID Connect

#### <a name="metadata-url"></a>Adresa URL metadat

Každý OpenID Connect zprostředkovatelů identity podle specifikace, popisuje dokumentu metadat, který obsahuje většinu informací potřebných k provedení přihlášení. To zahrnuje informace, jako jsou adresy URL k použití a umístění služby veřejné podpisového klíče. Dokument metadat OpenID Connect se nachází na koncový bod, který končí v vždy `.well-known\openid-configuration`.

Pro poskytovatele OpenID Connect identity Pokud chcete přidat, zadejte jeho adresu URL metadat.

#### <a name="client-id-and-secret"></a>ID klienta a tajný klíč

Povolit uživatelům umožní přihlásit, bude vyžadovat zprostředkovatele identity vývojáře pro registraci aplikace ve své službě. Tato aplikace bude mít ID (označované jako **ID klienta**) a **tajný kód klienta**. Zkopírujte tyto hodnoty od zprostředkovatele identity a zadejte do příslušných polí.

> [!NOTE]
> Tajný kód klienta je volitelný. Ale musíte zadat tajný klíč klienta, pokud chcete použít [tok autorizačního kódu](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), který používá tajný kód k výměně kód pro daný token.

#### <a name="scope"></a>Rozsah

Obory definovat informace a oprávnění, které pokud chcete shromažďovat z vašeho vlastního zprostředkovatele identity. Musí obsahovat OpenID Connect požadavky `openid` hodnotu oboru pro získání ID token od zprostředkovatele identity. Bez ID tokenu, uživatelé nebudou moct přihlásit do Azure AD B2C pomocí vlastního zprostředkovatele identity.

Další obory lze připojit (oddělte je mezerami). Dokumentaci vlastního zprostředkovatele identity Pokud chcete zobrazit, co může být jiné obory k dispozici.

#### <a name="response-type"></a>Typ odpovědi

Typ odpovědi, který popisuje, jaké informace se odešlou zpět v počáteční volání `authorization_endpoint` z vlastního zprostředkovatele identity. 

* `code`: Jak je uvedeno [tok autorizačního kódu](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), kód se vrátí zpět do Azure AD B2C. Azure AD B2C bude poté pokračujte volání `token_endpoint` k výměně kód pro daný token.
* `token`: Přístupový token se vrátí zpět do Azure AD B2C z vlastního zprostředkovatele identity.
* `id_token`: ID token bude vrácena zpět do Azure AD B2C z vlastního zprostředkovatele identity.


#### <a name="response-mode"></a>Režim odpovědi

Režim odpovědi definuje metodu, který se má použít k odesílání dat zpět z vlastního zprostředkovatele identity do Azure AD B2C.

* `form_post`: Tento režim odpovědi se doporučuje pro zvýšení zabezpečení. Odpověď se přenášejí prostřednictvím HTTP `POST` metoda s kódem nebo token zakódována pomocí textu `application/x-www-form-urlencoded` formátu.
* `query`: Kód nebo token se vrátí jako parametr dotazu.


#### <a name="domain-hint"></a>Nápověda domény

Nápověda domény je možné přeskočit přímo k přihlašovací stránce poskytovatele identitu, namísto toho, aby bylo možné uživatele vytvořit výběr v seznamu zprostředkovatelů identity k dispozici. Pokud chcete povolit tento druh chování, zadejte hodnotu pro nápovědu domény.

Chcete-li přejít do vlastního zprostředkovatele identity, přidejte parametr `domain_hint=<domain hint value>` na konci vaší žádosti při volání Azure AD B2C pro přihlášení.


### <a name="mapping-the-claims-from-the-openid-connect-identity-provider"></a>Mapování deklarace identity od zprostředkovatele identity OpenID Connect

Po vlastní identitu odešle zprostředkovatel tokenu ID zpět do Azure AD B2C, Azure AD B2C musí být schopen namapovat deklarace identity z přijatého tokenu na deklarace, které Azure AD B2C rozpozná a použije. 

Pro každou z níže uvedených mapování naleznete v dokumentaci vlastního zprostředkovatele identity pochopit deklarace identity, které jsou vráceny zpět v tokenech zprostředkovatele identity.

* `User ID`: Zadejte deklaraci identity, která poskytuje jedinečný identifikátor pro přihlášeného uživatele.
* `Display Name`: Zadejte deklaraci identity, která poskytuje zobrazované jméno nebo příjmení uživatele.
* `Given Name`: Zadejte deklaraci identity, která poskytuje křestní jméno uživatele.
* `Surname`: Zadejte deklarace identity, který obsahuje příjmení uživatele.
* `Email`: Zadejte deklaraci identity, která poskytuje e-mailovou adresu uživatele.

## <a name="next-steps"></a>Další postup

Přidat vlastní OpenID Connect zprostředkovatele identity pro vaše [integrované zásady](active-directory-b2c-reference-policies.md).
