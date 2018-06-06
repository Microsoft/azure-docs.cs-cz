---
title: Přidání OpenID Connect poskytovatelů identit v integrovaných zásad v Azure Active Directory B2C | Microsoft Docs
description: Průvodce přehled o tom, jak přidat zprostředkovatele OpenID Connect v předdefinované zásady služby Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/27/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e4325a7c87ac9975e819b22536838ec438fa1bcd
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709559"
---
# <a name="azure-active-directory-b2c-add-a-custom-openid-connect-identity-provider-in-built-in-policies"></a>Azure Active Directory B2C: Přidání vlastního zprostředkovatele identity OpenID Connect v integrovaných zásad

>[!NOTE]
> Tato funkce je ve verzi public preview. Nepoužívejte funkci v produkčním prostředí.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) je ověřovací protokol, nástavbou OAuth 2.0, který slouží k bezpečně přihlášení uživatele. Většina poskytovatelů identit, které používají tento protokol, jako například [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md), jsou podporovány v Azure AD B2C. Tento článek vysvětluje, jak můžete přidat vlastní zprostředkovatelé identity OpenID Connect do integrovaných zásad.

## <a name="configuring-a-custom-openid-connect-identity-provider"></a>Konfigurace vlastního zprostředkovatele identity OpenID Connect

Chcete-li přidat vlastního zprostředkovatele identity OpenID Connect:

1. Postupujte podle těchto kroků [přejděte do nastavení Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure.
1. Klikněte na **zprostředkovatelů Identity**.
1. Klikněte na **Přidat**.
1. Pro **typ zprostředkovatele Identity**, vyberte **OpenID Connect**.

### <a name="setting-up-the-openid-connect-identity-provider"></a>Nastavení zprostředkovatele identity OpenID Connect

#### <a name="metadata-url"></a>Adresa URL metadat

Každý OpenID Connect zprostředkovatelů identity podle specifikace, popisuje dokument metadat, který obsahuje většinu informace nezbytné k provedení přihlášení. To zahrnuje informace, jako jsou adresy URL používat a umístění služby veřejného podpisového klíče. Dokument metadat OpenID Connect se nachází na koncový bod, který končí v vždy `.well-known\openid-configuration`.

Pro zprostředkovatele identity OpenID Connect hledáte přidat, zadejte jeho adresu URL metadat.

#### <a name="client-id-and-secret"></a>ID klienta a tajný klíč

Povolit uživatelům přihlášení, bude vyžadovat zprostředkovatele identity vývojářům zaregistrovat aplikaci ve své služby. Tato aplikace bude mít ID, (označované jako **ID klienta**) a **tajný klíč klienta**. Zkopírujte tyto hodnoty od zprostředkovatele identity a zadejte je do odpovídajících polí.

> [!NOTE]
> Tajný klíč klienta je volitelný. Však musíte zadat tajný klíč klienta, pokud chcete použít [toku kódu autorizace](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), který používá tajný klíč pro výměnu kód pro daný token.

#### <a name="scope"></a>Rozsah

Obory definovat informace a oprávnění, která hledáte získat od poskytovatele vlastní identitu. Musí obsahovat OpenID Connect požadavky `openid` hodnotu oboru, aby bylo možné přijímat ID token od zprostředkovatele identity. Bez ID token, uživatelé nebudou moci přihlásit k Azure AD B2C pomocí zprostředkovatele vlastní identitu.

Další obory lze připojit (oddělené symbolem místa). V dokumentaci zprostředkovatele vlastní identity zobrazíte další obory, které je pravděpodobně k dispozici.

#### <a name="response-type"></a>Typ odpovědi

Typ odpovědi popisuje, jaký druh informací zašle zpět ve počáteční volání `authorization_endpoint` zprostředkovatele vlastní identitu. 

* `code`: Dle [toku kódu autorizace](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), kód bude vrácena zpět do Azure AD B2C. Azure AD B2C pak přejdete k volání `token_endpoint` Exchange kód pro daný token.
* `token`: Přístupový token se vrátí zpět do Azure AD B2C od zprostředkovatele vlastní identitu.
* `id_token`: ID token se vrátí zpět do Azure AD B2C od zprostředkovatele vlastní identitu.


#### <a name="response-mode"></a>Režim odpovědi

Režim odpovědi definuje metodu, která se používá k odesílání dat zpět z zprostředkovatele vlastní identity do Azure AD B2C.

* `form_post`: Tento režim odpovědi se doporučuje pro zvýšení zabezpečení. Odpověď se přenáší přes HTTP `POST` metoda pomocí kódu nebo token zakódování v textu pomocí `application/x-www-form-urlencoded` formátu.
* `query`: Kód nebo token, bude vrácen jako parametr dotazu.


#### <a name="domain-hint"></a>Nápověda domény

Pomocný parametr domény umožňuje přeskočit přímo na přihlašovací stránce zadanou identitou poskytovatele, místo nutnosti vytvořit uživatele s výběrem v seznamu zprostředkovatelů identity k dispozici. Pokud chcete povolit tento druh chování, zadejte hodnotu pro pomocný parametr domény.

Chcete-li přejít k poskytovateli vlastní identitu, připojte parametr `domain_hint=<domain hint value>` na konec vaši žádost o při volání metody Azure AD B2C pro přihlášení.


### <a name="mapping-the-claims-from-the-openid-connect-identity-provider"></a>Mapování deklarace identity od zprostředkovatele identity OpenID Connect

Po vlastní identity odešle zprostředkovatel tokenu ID zpět do Azure AD B2C, Azure AD B2C, musí být schopen mapování deklarace identity z přijatého tokenu deklarací identity, které Azure AD B2C rozpozná a použije. 

Pro každé mapování níže naleznete v dokumentaci zprostředkovatele vlastní identitu pochopit deklarace identity, které jsou vráceny zpět v tokenech zprostředkovatele identity.

* `User ID`: Zadejte deklarace identity, která poskytuje jedinečný identifikátor pro přihlášeného uživatele.
* `Display Name`: Zadejte deklarace identity, která poskytuje zobrazovaný název nebo úplný název pro uživatele.
* `Given Name`: Zadejte deklarace identity, která obsahuje jméno uživatele.
* `Surname`: Zadejte deklarace identity, která obsahuje příjmení uživatele.
* `Email`: Zadejte deklarace identity, která poskytuje e-mailovou adresu uživatele.

## <a name="next-steps"></a>Další postup

Přidat vlastní OpenID Connect poskytovatele identit pro vaše [předdefinovaných zásad](active-directory-b2c-reference-policies.md).
