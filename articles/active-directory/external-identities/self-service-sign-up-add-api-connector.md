---
title: Přidání konektorů rozhraní API k samoobslužným registračním tokům – Azure AD
description: Nakonfigurujte webové rozhraní API, které se má používat v toku uživatele.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: db68528a810ebc9cd61b205dd5167396d75db7f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613981"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>Přidání konektoru API do toku uživatele

Pokud chcete použít [konektor API](api-connectors-overview.md), vytvořte nejdřív konektor API a pak ho povolte v toku uživatele.

## <a name="create-an-api-connector"></a>Vytvoření konektoru API

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/) jako správce.
2. V části **služby Azure**vyberte **Azure Active Directory**.
3. V nabídce vlevo vyberte **externí identity**.
4. Vyberte **všechny konektory rozhraní API (Preview)** a pak vyberte **nový konektor API**.

   ![Přidání nového konektoru API](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. Zadejte zobrazovaný název pro volání. Například **Ověřte stav schválení**.
6. Zadejte **adresu URL koncového bodu** pro volání rozhraní API.
7. Zadejte ověřovací informace pro rozhraní API.

   - V tuto chvíli se podporuje jenom základní ověřování. Pokud chcete použít rozhraní API bez základního ověřování pro vývojové účely, stačí zadat fiktivní **uživatelské jméno** a **heslo** , které může vaše rozhraní API ignorovat. Pro použití s funkcí Azure s klíčem rozhraní API můžete kód zahrnout jako parametr dotazu v **adrese URL koncového bodu** (například https []() ://contoso.azurewebsites.NET/API/Endpoint<b>? Code = 0123456789</b>).

   ![Konfigurace nového konektoru API](./media/self-service-sign-up-add-api-connector/api-connector-config.png)
8. Vyberte **Uložit**.

> [!IMPORTANT]
> Dřív jste museli nakonfigurovat, které atributy uživatele se mají odeslat do rozhraní API (deklarace k odeslání), a které atributy uživatele se mají přijmout z rozhraní API (deklarace k přijetí). Nyní jsou všechny atributy uživatelů odesílány ve výchozím nastavení, pokud mají hodnotu a libovolný atribut uživatele může být vrácen rozhraním API v odpovědi "pokračování".

## <a name="the-request-sent-to-your-api"></a>Požadavek odeslaný do vašeho rozhraní API
Konektor rozhraní API se materializuje jako požadavek **http post** a jako páry klíč-hodnota se posílají atributy uživatele (deklarace), které jsou v těle JSON. Atributy jsou serializovány podobně jako [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/user#properties) vlastností uživatele. 

**Příklad požadavku**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

V požadavku jsou k dispozici pouze uživatelské vlastnosti a vlastní atributy uvedené v **Azure Active Directory**  >  **External Identities**  >  **vlastní uživatelské atributy** identit.

Vlastní atributy existují ve formátu **extension_ \<extensions-app-id> _AttributeName**  v adresáři. Rozhraní API by mělo očekávat deklarace identity v tomto stejném serializovaném formátu. Další informace o vlastních atributech najdete v tématu [Definice vlastních atributů pro vlastní toky podepisování](user-flow-add-custom-attributes.md).

Ve výchozím nastavení se ve všech požadavcích standardně odesílají deklarace identity **Locals (ui_locales) uživatelského rozhraní** . Poskytuje národní prostředí uživatele nastavené na zařízení, které může rozhraní API použít k vrácení mezinárodních odpovědí.

> [!IMPORTANT]
> Pokud deklarace identity pro odeslání nemá hodnotu v okamžiku volání koncového bodu rozhraní API, deklarace identity nebude odeslána do rozhraní API. Rozhraní API by mělo být navržené tak, aby explicitně kontrolovalo hodnotu, kterou očekává.

> [!TIP] 
> [**identity (identity)**](https://docs.microsoft.com/graph/api/resources/objectidentity) a deklarace **e-mailové adresy (' email ')** můžou vaše rozhraní API použít k identifikaci uživatele předtím, než budou mít účet ve vašem tenantovi. Deklarace identity identity se pošle, když se uživatel ověřuje pomocí zprostředkovatele identity, jako je Google nebo Facebook. ' e-mail ' je vždy odeslán.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Povolení konektoru API v toku uživatele

Pomocí těchto kroků přidáte konektor rozhraní API k samoobslužnému uživatelskému toku pro registraci.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/) jako správce.
2. V části **služby Azure**vyberte **Azure Active Directory**.
3. V nabídce vlevo vyberte **externí identity**.
4. Vyberte **toky uživatelů (Preview)** a pak vyberte tok uživatele, do kterého chcete konektor API přidat.
5. Vyberte možnost **konektory rozhraní API**a potom vyberte koncové body rozhraní API, které chcete vyvolat, v následujících krocích v toku uživatele:

   - **Po přihlášení pomocí zprostředkovatele identity**
   - **Před vytvořením uživatele**

   ![Přidání rozhraní API do toku uživatele](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. Vyberte **Uložit**.

## <a name="after-signing-in-with-an-identity-provider"></a>Po přihlášení pomocí zprostředkovatele identity

Konektor API v tomto kroku v procesu registrace se vyvolá hned po ověření uživatele u poskytovatele identity (Google, Facebook, Azure AD). Tento krok předchází ***stránku kolekce atributů***, což je formulář prezentovaný uživateli ke shromáždění atributů uživatele. 

<!-- The following are examples of API connector scenarios you may enable at this step:
- Use the email or federated identity that the user provided to look up claims in an existing system. Return these claims from the existing system, pre-fill the attribute collection page, and make them available to return in the token.
- Validate whether the user is included in an allow or deny list, and control whether they can continue with the sign-up flow. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>Příklad žádosti odeslané na rozhraní API v tomto kroku
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

Přesné deklarace identity odeslané na rozhraní API závisí na tom, jaké informace poskytovatel identity poskytuje. ' e-mail ' je vždy odeslán.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Očekávané typy odezvy z webového rozhraní API v tomto kroku

Když webové rozhraní API přijme požadavek HTTP z Azure AD během toku uživatele, může vrátit tyto odpovědi:

- Reakce na pokračování
- Blokování odpovědi

#### <a name="continuation-response"></a>Reakce na pokračování

Reakce na pokračování indikuje, že tok uživatele by měl pokračovat k dalšímu kroku: stránka kolekce atributů.

V reakci na pokračování může rozhraní API vracet deklarace identity. Pokud rozhraní API vrátí deklaraci identity, deklarace identity provede následující akce:

- Předem vyplní vstupní pole na stránce kolekce atributů.

Podívejte se na příklad [reakce na pokračování](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Blokování odpovědi

Odezva na blokování ukončuje tok uživatele. Může být záměrně vydaný rozhraním API, aby se zastavilo pokračování toku uživatelů tím, že se uživateli zobrazí stránka blokování. Stránka blokování zobrazuje `userMessage` rozhraní API, které poskytuje.

Podívejte se na příklad [blokující odezvy](#example-of-a-blocking-response).

## <a name="before-creating-the-user"></a>Před vytvořením uživatele

Konektor rozhraní API v tomto kroku v procesu registrace je vyvolán po stránce kolekce atributů, pokud je jeden zahrnutý. Tento krok se vždycky vyvolá před vytvořením uživatelského účtu ve službě Azure AD. 

<!-- The following are examples of scenarios you might enable at this point during sign-up: -->
<!-- 
- Validate user input data and ask a user to resubmit data.
- Block a user sign-up based on data entered by the user.
- Perform identity verification.
- Query external systems for existing data about the user and overwrite the user-provided value. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>Příklad žádosti odeslané na rozhraní API v tomto kroku

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```
Přesné deklarace identity odeslané na rozhraní API závisí na tom, které informace se shromažďují od uživatele nebo které poskytuje zprostředkovatel identity.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Očekávané typy odezvy z webového rozhraní API v tomto kroku

Když webové rozhraní API přijme požadavek HTTP z Azure AD během toku uživatele, může vrátit tyto odpovědi:

- Reakce na pokračování
- Blokování odpovědi
- Odpověď na ověření

#### <a name="continuation-response"></a>Reakce na pokračování

Reakce na pokračování indikuje, že tok uživatele by měl pokračovat k dalšímu kroku: vytvoření uživatele v adresáři.

V reakci na pokračování může rozhraní API vracet deklarace identity. Pokud rozhraní API vrátí deklaraci identity, deklarace identity provede následující akce:

- Přepíše všechny hodnoty, které již byly přiřazeny k deklaraci na stránce kolekce atributů.

Podívejte se na příklad [reakce na pokračování](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Blokování odpovědi
Odezva na blokování ukončuje tok uživatele. Může být záměrně vydaný rozhraním API, aby se zastavilo pokračování toku uživatelů tím, že se uživateli zobrazí stránka blokování. Stránka blokování zobrazuje `userMessage` rozhraní API, které poskytuje.

Podívejte se na příklad [blokující odezvy](#example-of-a-blocking-response).

### <a name="validation-error-response"></a>Ověření – chybová odezva
 Když rozhraní API odpoví odpověďmi na chybu ověřování, tok uživatele zůstane na stránce kolekce atributů a `userMessage` zobrazí se uživateli. Uživatel pak může formulář upravit a znovu odeslat. Tento typ odpovědi lze použít pro ověření vstupu.

Podívejte se na příklad [odpovědi na chybu ověřování](#example-of-a-validation-error-response).

## <a name="example-responses"></a>Příklady odpovědí

### <a name="example-of-a-continuation-response"></a>Příklad odpovědi na pokračování

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Parametr                                          | Typ              | Vyžadováno | Popis                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| verze                                            | Řetězec            | Yes      | Verze rozhraní API.                                                                                                                                                                                                                                                                |
| akce                                             | Řetězec            | Yes      | Hodnota musí být `Continue` .                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | No       | Hodnoty mohou být uloženy v adresáři, pokud jsou vybrány jako **deklarace pro příjem** v konfiguraci konektoru rozhraní API a **atributy uživatele** pro tok uživatele. Hodnoty mohou být vráceny v tokenu, pokud je vybrána jako **deklarace identity aplikace**.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | No       | Vrácená deklarace identity nemusí obsahovat `_<extensions-app-id>_` . Hodnoty se ukládají v adresáři, pokud se vybírají jako **deklarace, aby se přijímaly** v konfiguraci konektoru rozhraní API a **atributu uživatele** pro tok uživatele. Vlastní atributy se v tokenu nedají poslat zpátky. |

### <a name="example-of-a-blocking-response"></a>Příklad blokující odpovědi

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
    "code": "CONTOSO-BLOCK-00"
}

```

| Parametr   | Typ   | Vyžadováno | Popis                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| verze     | Řetězec | Yes      | Verze rozhraní API.                                                    |
| akce      | Řetězec | Yes      | Hodnota musí být `ShowBlockPage`                                              |
| userMessage | Řetězec | Yes      | Zpráva, která se zobrazí uživateli.                                            |
| kód        | Řetězec | No       | Kód chyby Lze použít pro účely ladění. Nezobrazuje se uživateli. |

**Činnost koncového uživatele s blokující odezvou**

![Příklad stránky bloku](./media/api-connectors-overview/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>Příklad ověřování – odpověď na chybu

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
    "code": "CONTOSO-VALIDATION-00"
}
```

| Parametr   | Typ    | Vyžadováno | Popis                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| verze     | Řetězec  | Yes      | Verze rozhraní API.                                                    |
| akce      | Řetězec  | Yes      | Hodnota musí být `ValidationError` .                                           |
| status      | Integer | Yes      | `400`Pro odpověď ValidationError musí být hodnota.                        |
| userMessage | Řetězec  | Yes      | Zpráva, která se zobrazí uživateli.                                            |
| kód        | Řetězec  | No       | Kód chyby Lze použít pro účely ladění. Nezobrazuje se uživateli. |

**Činnost koncového uživatele při ověřování – chybová odezva**

![Stránka příklad ověření](./media/api-connectors-overview/validation-error-postal-code.png)


## <a name="best-practices-and-how-to-troubleshoot"></a>Osvědčené postupy a řešení potíží

### <a name="using-serverless-cloud-functions"></a>Používání cloudových funkcí bez serveru
Funkce bez serveru, jako například triggery HTTP v Azure Functions, poskytují jednoduchý způsob vytváření koncových bodů rozhraní API pro použití s konektorem rozhraní API. Cloudovou funkci bez serveru můžete použít [například](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)k provedení logiky ověřování a omezení podpisů na konkrétní domény. Cloudová funkce bez serveru může také volat a volat další webová rozhraní API, uživatelská úložiště a další cloudové služby pro složitější scénáře.

### <a name="best-practices"></a>Osvědčené postupy
Zajistěte, aby:
* Vaše rozhraní API je za kontraktem požadavků a odpovědí rozhraní API, jak je uvedeno výše. 
* **Adresa URL koncového bodu** konektoru API odkazuje na správný koncový bod rozhraní API.
* Rozhraní API explicitně kontroluje hodnoty null přijatých deklarací.
* Vaše rozhraní API bude co nejrychleji reagovat, aby se zajistilo prostředí pro tekutiny uživatelů.
    * Pokud používáte funkci bez serveru nebo škálovatelnou webovou službu, použijte plán hostování, který uchovává rozhraní API "Start" nebo "teplou". U Azure Functions doporučujeme použít [Plán Premium](../../azure-functions/functions-scale.md#premium-plan). 


### <a name="use-logging"></a>Použití protokolování
Obecně je užitečné použít protokolovací nástroje povolené vaší službou webového rozhraní API, jako je [Application Insights](../../azure-functions/functions-monitoring.md), pro monitorování rozhraní API pro neočekávané kódy chyb, výjimky a špatný výkon.
* Monitorovat stavové kódy HTTP, které nejsou HTTP 200 nebo 400.
* Stavový kód HTTP 401 nebo 403 obvykle indikuje, že došlo k potížím s vaším ověřováním. V konektoru rozhraní API dvakrát ověřte vrstvu ověřování rozhraní API a odpovídající konfiguraci.
* V případě potřeby můžete v vývoji použít přísnější úrovně protokolování (např. "trace" nebo "debug").
* Sledujte své rozhraní API dlouhou dobu odezvy.

## <a name="next-steps"></a>Další kroky
<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- Přečtěte si, jak [Přidat vlastní pracovní postup schvalování do samoobslužné registrace](self-service-sign-up-add-approvals.md) .
- Začněte s našimi [ukázkami Azure Functions pro rychlý Start](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->
