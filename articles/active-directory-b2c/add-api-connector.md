---
title: Přidání konektorů rozhraní API do toků uživatelů (Preview)
description: Nakonfigurujte konektor API, který se má použít v toku uživatele.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: 47885e64b40db07ca3b4a7380389967a36abbd9e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949832"
---
# <a name="add-an-api-connector-to-a-sign-up-user-flow-preview"></a>Přidání konektoru rozhraní API k toku registrace uživatele (Preview)

> [!IMPORTANT]
> Konektory rozhraní API pro registraci jsou funkcí verze Public Preview služby Azure AD B2C. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

Pokud chcete použít [konektor API](api-connectors-overview.md), vytvořte nejdřív konektor API a pak ho povolte v toku uživatele.

## <a name="create-an-api-connector"></a>Vytvoření konektoru API

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. V části **služby Azure** vyberte **Azure AD B2C**.
4. Vyberte možnost **konektory rozhraní API (Preview)** a pak vyberte **nový konektor API**.

   ![Přidání nového konektoru API](./media/add-api-connector/api-connector-new.png)

5. Zadejte zobrazovaný název pro volání. Například **Ověřte informace o uživateli**.
6. Zadejte **adresu URL koncového bodu** pro volání rozhraní API.
7. Zadejte ověřovací informace pro rozhraní API.

   - V tuto chvíli se podporuje jenom základní ověřování. Pokud chcete použít rozhraní API bez základního ověřování pro vývojové účely, stačí zadat **uživatelské jméno** a **heslo** fiktivního uživatele, které může vaše rozhraní API ignorovat. Pro použití s funkcí Azure s klíčem rozhraní API můžete kód zahrnout jako parametr dotazu v **adrese URL koncového bodu** (například https []() ://contoso.azurewebsites.NET/API/Endpoint <b>? Code = 0123456789</b>).

   ![Konfigurace nového konektoru API](./media/add-api-connector/api-connector-config.png)
8. Vyberte **Uložit**.

## <a name="the-request-sent-to-your-api"></a>Požadavek odeslaný do vašeho rozhraní API
Konektor rozhraní API se materializuje jako požadavek **http post** a jako páry klíč-hodnota se posílají atributy uživatele (deklarace), které jsou v těle JSON. Atributy jsou serializovány podobně jako [Microsoft Graph](/graph/api/resources/user#properties) vlastností uživatele. 

**Příklad požadavku**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
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

V požadavku jsou k dispozici pouze vlastnosti uživatele a vlastní atributy uvedené v části **Azure AD B2C**  >  možnosti **atributů uživatele** .

Vlastní atributy existují ve formátu **extension_ \<extensions-app-id> _CustomAttribute**  v adresáři. Rozhraní API by mělo očekávat deklarace identity v tomto stejném serializovaném formátu. Další informace o vlastních atributech naleznete [v tématu Definování vlastních atributů v Azure Active Directory B2C](user-flow-custom-attributes.md).

Ve výchozím nastavení se ve všech požadavcích standardně odesílají deklarace identity **Locals (ui_locales) uživatelského rozhraní** . Poskytuje národní prostředí uživatele nastavené na zařízení, které může rozhraní API použít k vrácení mezinárodních odpovědí.

> [!IMPORTANT]
> Pokud deklarace identity nemá hodnotu v době volání koncového bodu rozhraní API, deklarace identity se nepošle do rozhraní API. Vaše rozhraní API by mělo být navržené tak, aby explicitně kontrolovalo a zpracovalo případ, ve kterém deklarace identity není v žádosti.

> [!TIP] 
> [**identity (identity)**](/graph/api/resources/objectidentity) a deklarace **e-mailové adresy (' email ')** můžou vaše rozhraní API použít k identifikaci uživatele předtím, než budou mít účet ve vašem tenantovi. 

## <a name="enable-the-api-connector-in-a-user-flow"></a>Povolení konektoru API v toku uživatele

Pomocí těchto kroků přidáte konektor rozhraní API do uživatelského toku registrace.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. V části **služby Azure** vyberte **Azure AD B2C**.
4. Vyberte **toky uživatelů** a pak vyberte tok uživatele, do kterého chcete konektor API přidat.
5. Vyberte možnost **konektory rozhraní API** a potom vyberte koncové body rozhraní API, které chcete vyvolat, v následujících krocích v toku uživatele:

   - **Po přihlášení pomocí zprostředkovatele identity**
   - **Před vytvořením uživatele**

   ![Přidání rozhraní API do toku uživatele](./media/add-api-connector/api-connectors-user-flow-select.png)

6. Vyberte **Uložit**.

## <a name="after-signing-in-with-an-identity-provider"></a>Po přihlášení pomocí zprostředkovatele identity

Konektor API v tomto kroku v procesu registrace se vyvolá hned po ověření uživatele s poskytovatelem identity (jako Google, Facebook, & Azure AD). Tento krok předchází *_stránku kolekce atributů_** _, což je formulář prezentovaný uživateli ke shromáždění atributů uživatele. Tento krok se nevyvolá, pokud se uživatel registruje pomocí místního účtu.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Příklad žádosti odeslané na rozhraní API v tomto kroku
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ 
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

Konektor rozhraní API v tomto kroku v procesu registrace je vyvolán po stránce kolekce atributů, pokud je jeden zahrnutý. Tento krok je vždy vyvolán před vytvořením uživatelského účtu.

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
 "identities": [
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
| \<builtInUserAttribute>                            | \<attribute-type> | Ne       | Vrácené hodnoty mohou přepsat hodnoty shromážděné uživatelem. Můžou se taky vracet v tokenu, pokud je vybraný jako deklarace _ * aplikace * *.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Ne       | Deklarace identity nemusí obsahovat `_<extensions-app-id>_` . Vrácené hodnoty mohou přepsat hodnoty shromážděné uživatelem. Můžou se taky vracet v tokenu, pokud je vybraný jako **deklarace identity aplikace**.  |

### <a name="example-of-a-blocking-response"></a>Příklad blokující odpovědi

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
}

```

| Parametr   | Typ   | Vyžadováno | Popis                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| verze     | Řetězec | Yes      | Verze rozhraní API.                                                    |
| akce      | Řetězec | Yes      | Hodnota musí být `ShowBlockPage`                                              |
| userMessage | Řetězec | Yes      | Zpráva, která se zobrazí uživateli.                                            |

**Činnost koncového uživatele s blokující odezvou**

![Příklad stránky bloku](./media/add-api-connector/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>Příklad ověřování – odpověď na chybu



```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code."
}
```

| Parametr   | Typ    | Vyžadováno | Popis                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| verze     | Řetězec  | Yes      | Verze rozhraní API.                                                    |
| akce      | Řetězec  | Yes      | Hodnota musí být `ValidationError` .                                           |
| status      | Integer | Yes      | `400`Pro odpověď ValidationError musí být hodnota.                        |
| userMessage | Řetězec  | Yes      | Zpráva, která se zobrazí uživateli.                                            |

> [!NOTE]
> Stavový kód HTTP musí být kromě hodnoty "stav" v těle odpovědi "400".

**Činnost koncového uživatele při ověřování – chybová odezva**

![Stránka příklad ověření](./media/add-api-connector/validation-error-postal-code.png)


## <a name="best-practices-and-how-to-troubleshoot"></a>Osvědčené postupy a řešení potíží

### <a name="using-serverless-cloud-functions"></a>Používání cloudových funkcí bez serveru
Funkce bez serveru, jako například triggery HTTP v Azure Functions, poskytují jednoduchý způsob vytváření koncových bodů rozhraní API pro použití s konektorem rozhraní API. Cloudovou funkci bez serveru můžete použít [například](code-samples.md#api-connectors)k provedení logiky ověřování a omezení podpisů na konkrétní e-mailové domény. Cloudová funkce bez serveru může také volat a volat další webová rozhraní API, uživatelská úložiště a další cloudové služby pro složitější scénáře.

### <a name="best-practices"></a>Osvědčené postupy
Zajistěte, aby:
* Vaše rozhraní API je za kontraktem požadavků a odpovědí rozhraní API, jak je uvedeno výše. 
* **Adresa URL koncového bodu** konektoru API odkazuje na správný koncový bod rozhraní API.
* Rozhraní API explicitně kontroluje hodnoty null přijatých deklarací.
* Vaše rozhraní API bude co nejrychleji reagovat, aby se zajistilo prostředí pro tekutiny uživatelů.
    * Pokud používáte funkci bez serveru nebo škálovatelnou webovou službu, použijte plán hostování, který uchovává rozhraní API "Start" nebo "teplou". v produkčním prostředí. Pro Azure Functions se doporučuje použít [Plán Premium](../azure-functions/functions-scale.md) .


### <a name="use-logging"></a>Použití protokolování
Obecně je užitečné použít protokolovací nástroje povolené vaší službou webového rozhraní API, jako je [Application Insights](../azure-functions/functions-monitoring.md), pro monitorování rozhraní API pro neočekávané kódy chyb, výjimky a špatný výkon.
* Monitorovat stavové kódy HTTP, které nejsou HTTP 200 nebo 400.
* Stavový kód HTTP 401 nebo 403 obvykle indikuje, že došlo k potížím s vaším ověřováním. V konektoru rozhraní API dvakrát ověřte vrstvu ověřování rozhraní API a odpovídající konfiguraci.
* V případě potřeby můžete v vývoji použít přísnější úrovně protokolování (např. "trace" nebo "debug").
* Sledujte své rozhraní API dlouhou dobu odezvy.

## <a name="next-steps"></a>Další kroky
<!-- - Learn how to [add a custom approval workflow to sign-up](add-approvals.md) -->
- Začněte s našimi [ukázkami](code-samples.md#api-connectors).
