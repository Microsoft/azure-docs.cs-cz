---
title: Přidání konektorů rozhraní API k samoobslužným registračním tokům – Azure AD
description: Nakonfigurujte webové rozhraní API, které se má používat v toku uživatele.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0498a2015b75221763ab5fdd4f6e94428922bd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85386738"
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

   ![Přidání nového konektoru API](./media/self-service-sign-up-add-api-connector/api-connector-config.png)

8. Vyberte deklarace identity, které chcete odeslat do rozhraní API.
9. Vyberte všechny deklarace identity, které plánujete z rozhraní API získat.

   ![Nastavit deklarace konektoru API](./media/self-service-sign-up-add-api-connector/api-connector-claims.png)

10. Vyberte **Uložit**.

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

Přečtěte si [, kde můžete povolit konektor API v toku uživatele](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow).

## <a name="request-sent-to-the-api"></a>Požadavek se odeslal do rozhraní API.

Konektor rozhraní API materializuje jako požadavek HTTP POST a posílá vybrané deklarace identity jako páry klíč-hodnota v těle JSON. Odpověď by měla obsahovat také hlavičku HTTP `Content-Type: application/json` . Atributy jsou serializovány podobně Microsoft Graph atributy uživatele. <!--# TODO: Add link to MS Graph or create separate reference.-->

### <a name="example-request"></a>Příklad požadavku

```http
POST <API-endpoint>
Content-type: application/json

{
 "email_address": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "postalCode": "33971",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

Deklarace identity **Locals (' ui_locales ') uživatelského rozhraní** je ve výchozím nastavení odeslána ve všech požadavcích. Poskytuje národní prostředí uživatele a může je používat rozhraní API k vrácení mezinárodních odpovědí. Nezobrazuje se v podokně Konfigurace rozhraní API.

Pokud deklarace identity pro odeslání nemá hodnotu v okamžiku volání koncového bodu rozhraní API, deklarace identity nebude odeslána do rozhraní API.

Vlastní atributy lze vytvořit pro uživatele pomocí **extension_ \<extensions-app-id> _AttributeName** formátu. Rozhraní API by mělo očekávat deklarace identity v tomto stejném serializovaném formátu. Vaše rozhraní API může vracet deklarace identity s nebo bez `<extensions-app-id>` . Další informace o vlastních atributech najdete v tématu [definování vlastních atributů pro vlastní toky podepisování](user-flow-add-custom-attributes.md).

> [!TIP] 
> [**identity (identity)**](https://docs.microsoft.com/graph/api/resources/objectidentity?view=graph-rest-1.0) a **e-mailová adresa (' email_address ')** můžete použít k identifikaci uživatele předtím, než budou mít účet ve vašem tenantovi. Deklarace identity identity se pošle, když se uživatel ověří s Google nebo Facebookem a email_address se vždycky pošle.

## <a name="expected-response-types-from-the-web-api"></a>Očekávané typy odezvy z webového rozhraní API

Když webové rozhraní API přijme požadavek HTTP z Azure AD během toku uživatele, může vrátit tyto odpovědi:

- [Reakce na pokračování](#continuation-response)
- [Blokování odpovědi](#blocking-response)
- [Ověření – chybová odezva](#validation-error-response)

### <a name="continuation-response"></a>Reakce na pokračování

Reakce na pokračování indikuje, že tok uživatele by měl pokračovat k dalšímu kroku. V reakci na pokračování může rozhraní API vracet deklarace identity.

Pokud rozhraní API vrátí deklaraci identity a vybere ji jako **deklaraci k přijetí**, deklarace identity provede následující akce:

- Předem vyplní vstupní pole na stránce kolekce atributů, pokud je konektor API vyvolán před zobrazením stránky.
- Přepíše všechny hodnoty, které již byly přiřazeny k deklaraci.
- Přiřadí deklaraci hodnoty, pokud byla dříve null.

#### <a name="example-of-a-continuation-response"></a>Příklad odpovědi na pokračování

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
| action                                             | Řetězec            | Yes      | Hodnota musí být `Continue` .                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | No       | Hodnoty mohou být uloženy v adresáři, pokud jsou vybrány jako **deklarace pro příjem** v konfiguraci konektoru rozhraní API a **atributy uživatele** pro tok uživatele. Hodnoty mohou být vráceny v tokenu, pokud je vybrána jako **deklarace identity aplikace**.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | No       | Vrácená deklarace může volitelně obsahovat `_<extensions-app-id>_` . Hodnoty se ukládají v adresáři, pokud se vybírají jako **deklarace, aby se přijímaly** v konfiguraci konektoru rozhraní API a **atributu uživatele** pro tok uživatele. Vlastní atributy se v tokenu nedají poslat zpátky. |

### <a name="blocking-response"></a>Blokování odpovědi

Odezva na blokování ukončuje tok uživatele. Může být záměrně vydaný rozhraním API, aby se zastavilo pokračování toku uživatelů tím, že se uživateli zobrazí stránka blokování. Stránka blokování zobrazuje `userMessage` rozhraní API, které poskytuje.

Níže je příklad blokující reakce:

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
| action      | Řetězec | Yes      | Hodnota musí být`ShowBlockPage`                                              |
| userMessage | Řetězec | Yes      | Zpráva, která se zobrazí uživateli.                                            |
| kód        | Řetězec | No       | Kód chyby Lze použít pro účely ladění. Nezobrazuje se uživateli. |

#### <a name="end-user-experience-with-a-blocking-response"></a>Činnost koncového uživatele s blokující odezvou

![Příklad stránky bloku](./media/api-connectors-overview/blocking-page-response.png)

### <a name="validation-error-response"></a>Ověření – chybová odezva

Volání rozhraní API volané po stránce kolekce atributů může vracet odpověď na chybu ověřování. V takovém případě tok uživatele zůstane na stránce kolekce atributů a `userMessage` zobrazí se uživateli. Uživatel pak může formulář upravit a znovu odeslat. Tento typ odpovědi lze použít pro ověření vstupu.

#### <a name="example-of-a-validation-error-response"></a>Příklad ověřování – odpověď na chybu

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
| action      | Řetězec  | Yes      | Hodnota musí být `ValidationError` .                                           |
| status      | Integer | Yes      | `400`Pro odpověď ValidationError musí být hodnota.                        |
| userMessage | Řetězec  | Yes      | Zpráva, která se zobrazí uživateli.                                            |
| kód        | Řetězec  | No       | Kód chyby Lze použít pro účely ladění. Nezobrazuje se uživateli. |

#### <a name="end-user-experience-with-a-validation-error-response"></a>Činnost koncového uživatele při ověření – chybová odezva

![Stránka příklad ověření](./media/api-connectors-overview/validation-error-postal-code.png)

### <a name="integration-with-azure-functions"></a>Integrace s Azure Functions
Trigger HTTP můžete v Azure Functions použít jako jednoduchý způsob, jak vytvořit rozhraní API pro použití s konektorem rozhraní API. Službu Azure Functions můžete použít [například](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)k provedení logiky ověřování a omezení podpisů na konkrétní domény. Můžete také volat a volat další webová rozhraní API, uživatelská úložiště a další cloudové služby.

## <a name="next-steps"></a>Další kroky

<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- Přečtěte si, jak [Přidat vlastní pracovní postup schvalování do samoobslužné registrace](self-service-sign-up-add-approvals.md) .
- Začněte s našimi [ukázkami Azure Functions pro rychlý Start](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->
