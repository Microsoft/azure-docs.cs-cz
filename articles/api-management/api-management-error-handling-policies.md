---
title: Zpracování chyb v zásadách Azure API Management | Microsoft Docs
description: Naučte se reagovat na chybové stavy, které můžou nastat při zpracování požadavků v Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 2c021a6d10c95b58ac444de8ea895ca01371a2b0
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902459"
---
# <a name="error-handling-in-api-management-policies"></a>Zpracování chyb v zásadách API Management

Když poskytnete `ProxyError` objekt, Azure API Management umožňuje vydavatelům reagovat na chybové podmínky, ke kterým může dojít během zpracování žádostí. Objekt `ProxyError` je k dispozici prostřednictvím [kontextu. Vlastnost sectionGroup](api-management-policy-expressions.md#ContextVariables) a lze ji použít v části zásady `on-error`. Tento článek poskytuje referenční informace o možnostech zpracování chyb v Azure API Management.

## <a name="error-handling-in-api-management"></a>Zpracování chyb v API Management

Zásady ve službě Azure API Management jsou rozdělené do oddílů `inbound`, `backend`, `outbound`a `on-error`, jak je znázorněno v následujícím příkladu.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is
         forwarded to the backend service go here -->
    </backend>
    <outbound>
      <!-- statements to be applied to the response go here -->
    </outbound>
    <on-error>
        <!-- statements to be applied if there is an error
             condition go here -->
  </on-error>
</policies>
```

Při zpracování požadavku se spustí integrovaný postup spolu se všemi zásadami, které jsou v rozsahu pro danou žádost. Pokud dojde k chybě, zpracování se okamžitě přeskočí do části zásady `on-error`.
Oddíl zásad `on-error` lze použít v jakémkoli oboru. Vydavatelé rozhraní API můžou nakonfigurovat vlastní chování, jako je například protokolování chyby do Center událostí nebo vytvoření nové odpovědi, která se vrátí volajícímu.

> [!NOTE]
> Ve výchozím nastavení se v zásadách nevyskytuje `on-error` oddíl. Pokud chcete přidat část `on-error` do zásad, přejděte k požadované zásadě v editoru zásad a přidejte ji. Další informace o konfiguraci zásad najdete v tématu [zásady v API Management](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).
>
> Pokud není k dispozici žádný `on-error` oddíl, volající obdrží zprávy s odpovědí HTTP 400 nebo 500, pokud dojde k chybě.

### <a name="policies-allowed-in-on-error"></a>Zásady povolené při zapnuté chybě

V části zásady `on-error` lze použít následující zásady.

-   [choose](api-management-advanced-policies.md#choose)
-   [set-variable](api-management-advanced-policies.md#set-variable)
-   [najít a nahradit](api-management-transformation-policies.md#Findandreplacestringinbody)
-   [návrat – odpověď](api-management-advanced-policies.md#ReturnResponse)
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)
-   [set-method](api-management-advanced-policies.md#SetRequestMethod)
-   [set-status](api-management-advanced-policies.md#SetStatus)
-   [Odeslat požadavek](api-management-advanced-policies.md#SendRequest)
-   [send-one-way-request](api-management-advanced-policies.md#SendOneWayRequest)
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)

## <a name="lasterror"></a>LastError

Pokud dojde k chybě a řízení přejde do části zásady `on-error`, je chyba uložena v [kontextu. Vlastnost poslední](api-management-policy-expressions.md#ContextVariables) , ke které se dají použít zásady v části `on-error` Poslední z těchto vlastností je.

| Name (Název)       | Typ   | Popis                                                                                               | Požaduje se |
| ---------- | ------ | --------------------------------------------------------------------------------------------------------- | -------- |
| `Source`   | string | Pojmenuje prvek, kde došlo k chybě. Může to být buď zásada, nebo vestavěný název kroku kanálu.      | Ano      |
| `Reason`   | string | Uživatelsky přívětivý kód chyby, který se dá použít při zpracování chyb.                                       | Ne       |
| `Message`  | string | Popis chyby čitelný člověkem.                                                                         | Ano      |
| `Scope`    | string | Název oboru, ve kterém došlo k chybě, a mohl by být typu "Global", "Product", "API" nebo "Operation" | Ne       |
| `Section`  | string | Název oddílu, kde došlo k chybě. Možné hodnoty: "příchozí", "back-end", "odchozí" nebo "On-Error".      | Ne       |
| `Path`     | string | Určuje vnořenou zásadu, například "Choose [3]/when [2]".                                                 | Ne       |
| `PolicyId` | string | Hodnota atributu `id`, pokud je zadána zákazníkem, podle zásad, kde došlo k chybě             | Ne       |

> [!TIP]
> Ke stavovým kódem můžete přistupovat prostřednictvím kontextu. Response. StatusCode

> [!NOTE]
> Všechny zásady mají volitelný `id` atribut, který se dá přidat do kořenového elementu zásady. Pokud je tento atribut přítomen v zásadě, pokud dojde k chybě, hodnota atributu může být načtena pomocí vlastnosti `context.LastError.PolicyId`.

## <a name="predefined-errors-for-built-in-steps"></a>Předdefinované chyby pro předdefinované kroky

Následující chyby jsou předdefinované pro chybové stavy, ke kterým může dojít během hodnocení integrovaných kroků zpracování.

| Zdroj        | Podmínka                                 | Důvod                  | Zpráva                                                                                                                |
| ------------- | ----------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| konfigurace | Identifikátor URI neodpovídá žádnému rozhraní API nebo operaci. | OperationNotFound       | Příchozí požadavek nelze spárovat s operací.                                                                      |
| authorization | Klíč předplatného není zadaný.             | SubscriptionKeyNotFound | Přístup byl odepřen z důvodu chybějícího klíče předplatného. Při vytváření požadavků na toto rozhraní API nezapomeňte zahrnout klíč předplatného. |
| authorization | Hodnota klíče předplatného není platná.         | SubscriptionKeyInvalid  | Přístup byl odepřen z důvodu neplatného klíče předplatného. Ujistěte se, že jste zadali platný klíč pro aktivní předplatné.            |
| multiple | Podřízené připojení (od klienta k bráně API Management) bylo klientem přerušeno, zatímco požadavek čekal. | ClientConnectionFailure | multiple |
| multiple | Nadřazené připojení (od API Management brány k back-end službě) se nevytvořilo nebo bylo přerušeno pomocí back-endu. | BackendConnectionFailure | multiple |
| multiple | Během hodnocení konkrétního výrazu došlo k výjimce za běhu. | ExpressionValueEvaluationFailure | multiple |

## <a name="predefined-errors-for-policies"></a>Předdefinované chyby pro zásady

Následující chyby jsou předdefinované pro chybové stavy, ke kterým může dojít při vyhodnocování zásad.

| Zdroj       | Podmínka                                                       | Důvod                    | Zpráva                                                                                                                              |
| ------------ | --------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| frekvence – omezení   | Překročení limitu přenosové rychlosti                                             | RateLimitExceeded         | Překročení limitu přenosové rychlosti                                                                                                               |
| kvóta        | Překročení kvóty                                                  | QuotaExceeded             | Překročení kvóty volání. Kvóta bude doplněna v XX: xx: xx. – nebo mimo kvótu šířky pásma. Kvóta bude doplněna v XX: xx: xx. |
| jsonp        | Hodnota parametru zpětného volání je neplatná (obsahuje nesprávné znaky). | CallbackParameterInvalid  | Hodnota parametru zpětného volání {callback-Parameter-Name} není platným identifikátorem JavaScriptu.                                          |
| ip-filter    | Nepovedlo se analyzovat IP adresu volajícího z požadavku.                          | FailedToParseCallerIP     | Nepovedlo se vytvořit IP adresu volajícího. Přístup se odepřel.                                                                        |
| ip-filter    | IP adresa volajícího není v seznamu povolených.                                | CallerIpNotAllowed        | IP adresa volajícího {IP-Address} není povolená. Přístup se odepřel.                                                                        |
| ip-filter    | Volající je v seznamu blokovaných IP adres.                                    | CallerIpBlocked           | IP adresa volajícího je blokovaná. Přístup se odepřel.                                                                                         |
| check-Header | Není předložena požadovaná hlavička nebo chybí hodnota.               | HeaderNotFound            | Záhlaví {Header-Name} nebylo v požadavku nalezeno. Přístup se odepřel.                                                                    |
| check-Header | Není předložena požadovaná hlavička nebo chybí hodnota.               | HeaderValueNotAllowed     | Hodnota hlavičky {Header-Name} {Header-value} není povolená. Přístup se odepřel.                                                          |
| validate-jwt | Token JWT v žádosti chybí.                                 | TokenNotFound             | V požadavku se nenašel token JWT. Přístup se odepřel.                                                                                         |
| validate-jwt | Podpis se nepovedlo ověřit.                                     | TokenSignatureInvalid     | \>< zpráva z knihovny JWT. Přístup se odepřel.                                                                                          |
| validate-jwt | Neplatná cílová skupina                                                | TokenAudienceNotAllowed   | \>< zpráva z knihovny JWT. Přístup se odepřel.                                                                                          |
| validate-jwt | Neplatný Vydavatel                                                  | TokenIssuerNotAllowed     | \>< zpráva z knihovny JWT. Přístup se odepřel.                                                                                          |
| validate-jwt | Vypršela platnost tokenu.                                                   | TokenExpired              | \>< zpráva z knihovny JWT. Přístup se odepřel.                                                                                          |
| validate-jwt | Podpisový klíč nebyl přeložen podle ID                            | TokenSignatureKeyNotFound | \>< zpráva z knihovny JWT. Přístup se odepřel.                                                                                          |
| validate-jwt | V tokenu chybí požadované deklarace identity.                          | TokenClaimNotFound        | V tokenu JWT chybí následující deklarace: < C1\>, < C2\>,... Přístup se odepřel.                                                            |
| validate-jwt | Neshoda hodnot deklarace identity                                           | TokenClaimValueNotAllowed | Hodnota deklarace identity {Claim-Name} typu {Claim-value} není povolená. Přístup se odepřel.                                                             |
| validate-jwt | Jiná selhání ověřování                                       | JwtInvalid                | < zpráva z knihovny JWT\>                                                                                                          |
| předat požadavek nebo odeslat požadavek | V nakonfigurovaném časovém limitu se nepřijal kód stavu odpovědi HTTP a hlavičky z back-endu. | časový limit | multiple |

## <a name="example"></a>Příklad:

Nastavení zásad rozhraní API pro:

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <set-header name="ErrorSource" exists-action="override">
            <value>@(context.LastError.Source)</value>
        </set-header>
        <set-header name="ErrorReason" exists-action="override">
            <value>@(context.LastError.Reason)</value>
        </set-header>
        <set-header name="ErrorMessage" exists-action="override">
            <value>@(context.LastError.Message)</value>
        </set-header>
        <set-header name="ErrorScope" exists-action="override">
            <value>@(context.LastError.Scope)</value>
        </set-header>
        <set-header name="ErrorSection" exists-action="override">
            <value>@(context.LastError.Section)</value>
        </set-header>
        <set-header name="ErrorPath" exists-action="override">
            <value>@(context.LastError.Path)</value>
        </set-header>
        <set-header name="ErrorPolicyId" exists-action="override">
            <value>@(context.LastError.PolicyId)</value>
        </set-header>
        <set-header name="ErrorStatusCode" exists-action="override">
            <value>@(context.Response.StatusCode.ToString())</value>
        </set-header>
        <base />
    </on-error>
</policies>
```

a odeslání neautorizované žádosti bude mít za následek následující odpověď:

![Neautorizovaná odpověď na chybu](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami najdete v těchto tématech:

-   [Zásady v API Management](api-management-howto-policies.md)
-   [Transformační rozhraní API](transform-api.md)
-   [Odkaz na zásady](api-management-policy-reference.md) pro úplný seznam příkazů zásad a jejich nastavení
-   [Ukázky zásad](policy-samples.md)
