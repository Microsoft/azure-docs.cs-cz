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
ms.openlocfilehash: facdb99a49c3778a75e733abf1fc72eed67549ab
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611606"
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
7. Vyberte **typ ověřování** a nakonfigurujte ověřovací informace pro volání rozhraní API. Možnosti zabezpečení rozhraní API najdete níže v části.

    ![Konfigurace konektoru API](./media/add-api-connector/api-connector-config.png)

8. Vyberte **Uložit**.

## <a name="securing-the-api-endpoint"></a>Zabezpečení koncového bodu rozhraní API
Koncový bod rozhraní API můžete chránit buď pomocí základního ověřování HTTP, nebo pomocí ověřování klientského certifikátu HTTPS (Preview). V obou případech poskytnete přihlašovací údaje, které Azure AD B2C použijí při volání koncového bodu rozhraní API. Váš koncový bod rozhraní API pak zkontroluje přihlašovací údaje a provede rozhodnutí o autorizaci.

### <a name="http-basic-authentication"></a>Základní ověřování HTTP
Základní ověřování HTTP je definované v [dokumentu RFC 2617](https://tools.ietf.org/html/rfc2617). Azure AD B2C odešle požadavek HTTP s přihlašovacími údaji klienta ( `username` a `password` ) v `Authorization` hlavičce. Pověření jsou formátována jako řetězec kódovaný v kódování Base64 `username:password` . Rozhraní API pak tyto hodnoty zkontroluje, aby bylo možné určit, zda má být volání rozhraní API odmítnuto.

### <a name="https-client-certificate-authentication-preview"></a>Ověřování klientským certifikátem HTTPS (Preview)

> [!IMPORTANT]
> Tato funkce je ve verzi Preview a je k dispozici bez smlouvy o úrovni služeb. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ověřování klientským certifikátem je vzájemné ověřování založené na certifikátech, kde klient poskytuje klientský certifikát pro ověření jeho identity na serveru. V takovém případě bude Azure AD B2C používat certifikát, který nahrajete jako součást konfigurace konektoru rozhraní API. K tomu dochází jako součást metody handshake protokolu SSL. K vaší službě REST API mají přístup pouze služby, které mají správné certifikáty. Certifikát klienta je digitální certifikát X. 509. V produkčním prostředí by měl být podepsaný certifikační autoritou. 


Certifikát můžete vytvořit pomocí [Azure Key Vault](../key-vault/certificates/create-certificate.md), který obsahuje možnosti pro certifikáty podepsané svým držitelem a integrace s poskytovateli vystavitelů certifikátů pro podepsané certifikáty. Pak můžete [certifikát exportovat](../key-vault/certificates/how-to-export-certificate.md) a nahrát ho pro použití v konfiguraci konektorů rozhraní API. Všimněte si, že heslo je vyžadováno jenom pro soubory certifikátů chráněné heslem. K vygenerování certifikátu podepsaného svým držitelem můžete použít také [rutinu New-SelfSignedCertificate](./secure-rest-api.md#prepare-a-self-signed-certificate-optional) prostředí PowerShell.

Azure App Service a Azure Functions najdete v tématu [Konfigurace vzájemného ověřování TLS](../app-service/app-service-web-configure-tls-mutual-auth.md) , kde se dozvíte, jak povolit a ověřit certifikát z koncového bodu rozhraní API.

Doporučuje se nastavit upozornění na připomenutí, kdy vyprší platnost certifikátu. Pokud chcete nahrát nový certifikát do existujícího konektoru API, vyberte konektor API v části **konektory rozhraní API (Preview)** a klikněte na **nahrát nový certifikát**. Poslední nahraný certifikát, jehož platnost vypršela, a který je po datu zahájení, bude automaticky použit Azure AD B2C.

### <a name="api-key"></a>Klíč rozhraní API
Některé služby používají mechanismus "klíč rozhraní API", což usnadňuje přístup k koncovým bodům HTTP během vývoje. V případě [Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)to můžete udělat tak, že `code` do **adresy URL koncového bodu** zadáte jako parametr dotazu. Například `https://contoso.azurewebsites.net/api/endpoint` <b>`?code=0123456789`</b> ). 

Nejedná se o mechanismus, který by se měl použít samostatně v produkčním prostředí. Konfigurace pro základní ověřování nebo ověřování certifikátů je proto vždy nutná. Pokud chcete pro účely vývoje implementovat jakoukoli metodu ověřování (nedoporučuje se), můžete zvolit základní ověřování a použít dočasné hodnoty pro `username` a `password` to, že vaše rozhraní API může při implementaci autorizace ve vašem rozhraní API ignorovat.

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

Vlastní atributy existují ve formátu **extension_ \<extensions-app-id> _CustomAttribute**  v adresáři. Rozhraní API by mělo očekávat deklarace identity v tomto stejném serializovaném formátu. Další informace o vlastních atributech naleznete [v tématu Definování vlastních atributů v Azure AD B2C](user-flow-custom-attributes.md).

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

Konektor API v tomto kroku v procesu registrace se vyvolá hned po ověření uživatele s poskytovatelem identity (jako Google, Facebook, & Azure AD). Tento krok předchází ***stránku kolekce atributů***, což je formulář prezentovaný uživateli ke shromáždění atributů uživatele. Tento krok se nevyvolá, pokud se uživatel registruje pomocí místního účtu.

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
| akce                                             | Řetězec            | Yes      | Hodnota musí být `Continue` .                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | No       | Vrácené hodnoty mohou přepsat hodnoty shromážděné uživatelem. Můžou se taky vracet v tokenu, pokud je vybraný jako **deklarace identity aplikace**.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | No       | Deklarace identity nemusí obsahovat `_<extensions-app-id>_` . Vrácené hodnoty mohou přepsat hodnoty shromážděné uživatelem. Můžou se taky vracet v tokenu, pokud je vybraný jako **deklarace identity aplikace**.  |

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
| verze     | Řetězec  | Yes      | Verze vašeho rozhraní API.                                                    |
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
- Začněte s našimi [ukázkami](code-samples.md#api-connectors).
