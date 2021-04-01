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
ms.openlocfilehash: a3b6f90d0aa26b478c0f2fcefac55dcd509da437
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92070941"
---
# <a name="error-handling-in-api-management-policies"></a>Zpracování chyb v zásadách služby API Management

Poskytnutím `ProxyError` objektu Azure API Management umožňuje vydavatelům reagovat na chybové podmínky, ke kterým může dojít během zpracování požadavků. K `ProxyError` objektu je přistup prostřednictvím [kontextu. Vlastnost](api-management-policy-expressions.md#ContextVariables) sectionGroup a dá se použít v zásadách v `on-error` části Policy (zásady). Tento článek poskytuje referenční informace o možnostech zpracování chyb v Azure API Management.

## <a name="error-handling-in-api-management"></a>Zpracování chyb v API Management

Zásady ve službě Azure API Management jsou rozdělené do `inbound` `backend` oddílů,, `outbound` a, `on-error` jak je znázorněno v následujícím příkladu.

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

Při zpracování požadavku se spustí integrovaný postup spolu se všemi zásadami, které jsou v rozsahu pro danou žádost. Pokud dojde k chybě, zpracování se okamžitě přeskočí do `on-error` oddílu zásad.
`on-error`Oddíl zásad lze použít v jakémkoli oboru. Vydavatelé rozhraní API můžou nakonfigurovat vlastní chování, jako je například protokolování chyby do Center událostí nebo vytvoření nové odpovědi, která se vrátí volajícímu.

> [!NOTE]
> Oddíl není ve `on-error` výchozím nastavení v zásadách k dispozici. Pokud chcete přidat `on-error` oddíl do zásad, přejděte na požadovanou zásadu v editoru zásad a přidejte ji. Další informace o konfiguraci zásad najdete v tématu [zásady v API Management](./api-management-howto-policies.md).
>
> Pokud není k dispozici žádný `on-error` oddíl, volající obdrží zprávy s odpovědí HTTP 400 nebo 500, pokud dojde k chybovému stavu.

### <a name="policies-allowed-in-on-error"></a>Zásady povolené při zapnuté chybě

V části zásady se dají použít tyto zásady `on-error` .

-   [výběrem](api-management-advanced-policies.md#choose)
-   [SET – proměnná](api-management-advanced-policies.md#set-variable)
-   [najít a nahradit](api-management-transformation-policies.md#Findandreplacestringinbody)
-   [návrat – odpověď](api-management-advanced-policies.md#ReturnResponse)
-   [Set – hlavička](api-management-transformation-policies.md#SetHTTPheader)
-   [Set – metoda](api-management-advanced-policies.md#SetRequestMethod)
-   [nastavit stav](api-management-advanced-policies.md#SetStatus)
-   [Odeslat požadavek](api-management-advanced-policies.md#SendRequest)
-   [Send – One-Way – požadavek](api-management-advanced-policies.md#SendOneWayRequest)
-   [přihlášení k centru událostí](api-management-advanced-policies.md#log-to-eventhub)
-   [JSON-to-XML](api-management-transformation-policies.md#ConvertJSONtoXML)
-   [z kódu XML do formátu JSON](api-management-transformation-policies.md#ConvertXMLtoJSON)
-   [limit – souběžnost](api-management-advanced-policies.md#LimitConcurrency)
-   [Maketa – odezva](api-management-advanced-policies.md#mock-response)
-   [Opakujte](api-management-advanced-policies.md#Retry)
-   [Přehled](api-management-advanced-policies.md#Trace)

## <a name="lasterror"></a>LastError

Pokud dojde k chybě a řízení přejde na `on-error` oddíl zásady, je chyba uložena v [kontextu. Vlastnost GetLastError](api-management-policy-expressions.md#ContextVariables) , ke které se dají přistupovat zásady v `on-error` části. Poslední z těchto vlastností je.

| Název       | Typ   | Popis                                                                                               | Povinné |
| ---------- | ------ | --------------------------------------------------------------------------------------------------------- | -------- |
| `Source`   | řetězec | Pojmenuje prvek, kde došlo k chybě. Může to být buď zásada, nebo vestavěný název kroku kanálu.      | Yes      |
| `Reason`   | řetězec | Uživatelsky přívětivý kód chyby, který se dá použít při zpracování chyb.                                       | No       |
| `Message`  | řetězec | Popis chyby čitelný člověkem.                                                                         | Yes      |
| `Scope`    | řetězec | Název oboru, ve kterém došlo k chybě, a mohl by být typu "Global", "Product", "API" nebo "Operation" | No       |
| `Section`  | řetězec | Název oddílu, kde došlo k chybě. Možné hodnoty: "příchozí", "back-end", "odchozí" nebo "On-Error".      | No       |
| `Path`     | řetězec | Určuje vnořenou zásadu, například "Choose [3]/when [2]".                                                 | No       |
| `PolicyId` | řetězec | Hodnota `id` atributu, pokud je zadána zákazníkem, podle zásad, kde došlo k chybě             | No       |

> [!TIP]
> Ke stavovým kódem můžete přistupovat prostřednictvím kontextu. Response. StatusCode

> [!NOTE]
> Všechny zásady mají volitelný `id` atribut, který lze přidat do kořenového prvku zásady. Pokud je tento atribut přítomen v zásadě, pokud dojde k chybě, hodnota atributu může být načtena pomocí `context.LastError.PolicyId` Vlastnosti.

## <a name="predefined-errors-for-built-in-steps"></a>Předdefinované chyby pro předdefinované kroky

Následující chyby jsou předdefinované pro chybové stavy, ke kterým může dojít během hodnocení integrovaných kroků zpracování.

| Zdroj        | Podmínka                                 | Důvod                  | Zpráva                                                                                                                |
| ------------- | ----------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| konfigurace | Identifikátor URI neodpovídá žádnému rozhraní API nebo operaci. | OperationNotFound       | Příchozí požadavek nelze spárovat s operací.                                                                      |
| autorizace | Klíč předplatného není zadaný.             | SubscriptionKeyNotFound | Přístup byl odepřen z důvodu chybějícího klíče předplatného. Při vytváření požadavků na toto rozhraní API nezapomeňte zahrnout klíč předplatného. |
| autorizace | Hodnota klíče předplatného není platná.         | SubscriptionKeyInvalid  | Přístup byl odepřen z důvodu neplatného klíče předplatného. Ujistěte se, že jste zadali platný klíč pro aktivní předplatné.            |
| multiple | Podřízené připojení (od klienta k bráně API Management) bylo klientem přerušeno, zatímco požadavek čekal. | ClientConnectionFailure | multiple |
| multiple | Nadřazené připojení (od API Management brány k back-end službě) se nevytvořilo nebo bylo přerušeno pomocí back-endu. | BackendConnectionFailure | multiple |
| multiple | Během hodnocení konkrétního výrazu došlo k výjimce za běhu. | ExpressionValueEvaluationFailure | multiple |

## <a name="predefined-errors-for-policies"></a>Předdefinované chyby pro zásady

Následující chyby jsou předdefinované pro chybové stavy, ke kterým může dojít při vyhodnocování zásad.

| Zdroj       | Podmínka                                                       | Důvod                    | Zpráva                                                                                                                              |
| ------------ | --------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| frekvence – omezení   | Překročení limitu přenosové rychlosti                                             | RateLimitExceeded         | Překročení limitu přenosové rychlosti                                                                                                               |
| kvóta        | Překročení kvóty                                                  | QuotaExceeded             | Překročení kvóty volání. Kvóta bude doplněna v XX: xx: xx. – nebo mimo kvótu šířky pásma. Kvóta bude doplněna v XX: xx: xx. |
| JSONP        | Hodnota parametru zpětného volání je neplatná (obsahuje nesprávné znaky). | CallbackParameterInvalid  | Hodnota parametru zpětného volání {callback-Parameter-Name} není platným identifikátorem JavaScriptu.                                          |
| filtr IP adres    | Nepovedlo se analyzovat IP adresu volajícího z požadavku.                          | FailedToParseCallerIP     | Nepovedlo se vytvořit IP adresu volajícího. Přístup se odepřel.                                                                        |
| filtr IP adres    | IP adresa volajícího není v seznamu povolených.                                | CallerIpNotAllowed        | IP adresa volajícího {IP-Address} není povolená. Přístup se odepřel.                                                                        |
| filtr IP adres    | Volající je v seznamu blokovaných IP adres.                                    | CallerIpBlocked           | IP adresa volajícího je blokovaná. Přístup se odepřel.                                                                                         |
| check-Header | Není předložena požadovaná hlavička nebo chybí hodnota.               | HeaderNotFound            | Záhlaví {Header-Name} nebylo v požadavku nalezeno. Přístup se odepřel.                                                                    |
| check-Header | Není předložena požadovaná hlavička nebo chybí hodnota.               | HeaderValueNotAllowed     | Hodnota hlavičky {Header-Name} {Header-value} není povolená. Přístup se odepřel.                                                          |
| ověřit – JWT | Token JWT v žádosti chybí.                                 | TokenNotFound             | V požadavku se nenašel token JWT. Přístup se odepřel.                                                                                         |
| ověřit – JWT | Podpis se nepovedlo ověřit.                                     | TokenSignatureInvalid     | <zpráva z knihovny JWT \> . Přístup se odepřel.                                                                                          |
| ověřit – JWT | Neplatná cílová skupina                                                | TokenAudienceNotAllowed   | <zpráva z knihovny JWT \> . Přístup se odepřel.                                                                                          |
| ověřit – JWT | Neplatný Vydavatel                                                  | TokenIssuerNotAllowed     | <zpráva z knihovny JWT \> . Přístup se odepřel.                                                                                          |
| ověřit – JWT | Vypršela platnost tokenu.                                                   | TokenExpired              | <zpráva z knihovny JWT \> . Přístup se odepřel.                                                                                          |
| ověřit – JWT | Podpisový klíč nebyl přeložen podle ID                            | TokenSignatureKeyNotFound | <zpráva z knihovny JWT \> . Přístup se odepřel.                                                                                          |
| ověřit – JWT | V tokenu chybí požadované deklarace identity.                          | TokenClaimNotFound        | V tokenu JWT chybí následující deklarace: <C1 \> , <C2 \> ,... Přístup se odepřel.                                                            |
| ověřit – JWT | Neshoda hodnot deklarace identity                                           | TokenClaimValueNotAllowed | Hodnota deklarace identity {Claim-Name} typu {Claim-value} není povolená. Přístup se odepřel.                                                             |
| ověřit – JWT | Jiná selhání ověřování                                       | JwtInvalid                | <zpráva z knihovny JWT\>                                                                                                          |
| předat požadavek nebo odeslat požadavek | V nakonfigurovaném časovém limitu se nepřijal kód stavu odpovědi HTTP a hlavičky z back-endu. | Časový limit | multiple |

## <a name="example"></a>Příklad

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
-   [Odkaz na zásady](./api-management-policies.md) pro úplný seznam příkazů zásad a jejich nastavení
-   [Ukázky zásad](./policy-reference.md)