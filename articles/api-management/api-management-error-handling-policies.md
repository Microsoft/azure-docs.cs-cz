---
title: Zpracování chyb v zásady služby Azure API Management | Dokumentace Microsoftu
description: Zjistěte, jak reagovat na chybové stavy, které mohou nastat během zpracování požadavků ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: apimpm
ms.openlocfilehash: 2bde63bb668188936b3dd3cf5ecbf3b8c604eb95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564308"
---
# <a name="error-handling-in-api-management-policies"></a>Zpracování chyb v zásady služby API Management

Tím, že poskytuje `ProxyError` objektu Azure API Management umožňuje vydavatelům reagovat na chybové stavy, které mohou nastat při zpracování požadavků. `ProxyError` Je objekt zpřístupněný prostřednictvím [kontextu. LastError](api-management-policy-expressions.md#ContextVariables) vlastnost a můžou používat zásady `on-error` části zásady. Tento článek poskytuje odkaz pro chybu funkce zpracování ve službě Azure API Management.  
  
## <a name="error-handling-in-api-management"></a>Chyba zpracování ve službě API Management

 Zásady ve službě Azure API Management jsou rozdělené do `inbound`, `backend`, `outbound`, a `on-error` sections – jak je znázorněno v následujícím příkladu.  
  
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
  
Během zpracování žádosti o provádění předdefinované kroků spolu se všechny zásady, které jsou v oboru pro daný požadavek. Pokud dojde k chybě, zpracování okamžitě přejde `on-error` části zásady.  
`on-error` Zásady je možné v oboru. Vydavatelé rozhraní API můžete nakonfigurovat vlastní chování, jako je například protokolování chyba do služby event hubs nebo vytváří nové odpovědi za účelem vrácení volajícímu.  
  
> [!NOTE]
>  `on-error` Části není k dispozici v zásadách ve výchozím nastavení. Chcete-li přidat `on-error` k zásadě, přejděte na požadovanou zásadu v editoru zásad a přidejte ji. Další informace o konfiguraci zásad najdete v tématu [zásady ve službě API Management](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).  
>   
>  Pokud není žádný `on-error` části volající bude přijímat zpráv odpovědí HTTP 400 nebo 500, pokud dojde k chybě.  
  
### <a name="policies-allowed-in-on-error"></a>Zásady povolené na chybu

 Tyto zásady dají použít v `on-error` části zásady.  
  
-   [choose](api-management-advanced-policies.md#choose)  
-   [set-variable](api-management-advanced-policies.md#set-variable)  
-   [find-and-replace](api-management-transformation-policies.md#Findandreplacestringinbody)  
-   [vrácení odpovědi](api-management-advanced-policies.md#ReturnResponse)  
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)  
-   [set-method](api-management-advanced-policies.md#SetRequestMethod)  
-   [set-status](api-management-advanced-policies.md#SetStatus)  
-   [send-request](api-management-advanced-policies.md#SendRequest)  
-   [send-one-way-request](api-management-advanced-policies.md#SendOneWayRequest)  
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)  
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)  
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)  
  
## <a name="lasterror"></a>LastError

 Když dojde k chybě a řízení přejde `on-error` části zásady, chyba je uložen v [kontextu. LastError](api-management-policy-expressions.md#ContextVariables) vlastnost, která je přístupná v rámci zásad v `on-error` oddílu. LastError má následující vlastnosti.  
  
| Název     | Typ   | Popis                                                                                               | Požaduje se |
|----------|--------|-----------------------------------------------------------------------------------------------------------|----------|
| Source   | string | Název elementu, kde došlo k chybě. Může být zásad nebo název kroku integrované kanálu.     | Ano      |
| Reason   | string | Kód chyby přívětivá počítač, který by se použil při zpracování chyb.                                       | Ne       |
| Message  | string | Popis chyby čitelné.                                                                         | Ano      |
| Scope    | string | Název oboru, ve kterém chyba došlo k chybě a můžou mít jednu z "globální", "produkt", "rozhraní api" nebo "operace" | Ne       |
| Section  | string | Název oddílu, ve kterém došlo k chybě. Možné hodnoty: "příchozí", "backend", "výstupní" nebo "na chybu".       | Ne       |
| `Path`     | string | Určuje vnořené zásady, třeba "zvolte [3] / při [2]".                                                        | Ne       |
| `PolicyId` | string | Hodnota `id` atribut, pokud zadaný výhradně zákazník, a to na zásadách, kde došlo k chybě             | Ne       |

> [!TIP]
> Stavový kód můžete přistupovat prostřednictvím kontextu. Response.StatusCode.  

> [!NOTE]
> Všechny zásady mít volitelně `id` atribut, který lze přidat do kořenového prvku zásad. Pokud tento atribut je k dispozici v zásadách, když dojde k chybě, je možné hodnotu atributu načíst pomocí `context.LastError.PolicyId` vlastnost.

## <a name="predefined-errors-for-built-in-steps"></a>Předdefinované chyby pro vestavěné kroky  
 Tyto chyby jsou předdefinované pro chybové podmínky, které mohou nastat během vyhodnocení vestavěné kroky.  
  
| Source        | Podmínka                                 | Reason                  | Message                                                                                                                |
|---------------|-------------------------------------------|-------------------------|------------------------------------------------------------------------------------------------------------------------|
| konfigurace | Identifikátor URI neodpovídá žádné operaci nebo rozhraní API | OperationNotFound       | Nelze spárovat příchozího požadavku pro operaci.                                                                      |
| Autorizace | Není zadaný klíč předplatného             | SubscriptionKeyNotFound | Přístup byl odepřen z důvodu chybějícího klíč předplatného. Ujistěte se, že při zasílání požadavků na toto rozhraní API obsahovat klíč předplatného. |
| Autorizace | Hodnota klíče předplatného není platná.         | SubscriptionKeyInvalid  | Přístup byl odepřen z důvodu neplatné předplatné klíč. Je nutné zadat platný klíč pro aktivní předplatné.            |
  
## <a name="predefined-errors-for-policies"></a>Předdefinované chyby pro zásady  
 Tyto chyby jsou předdefinované pro chybové podmínky, které se můžou objevit během vyhodnocení zásad.  
  
| Source       | Podmínka                                                       | Reason                    | Message                                                                                                                              |
|--------------|-----------------------------------------------------------------|---------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| omezení četnosti   | Překročil se limit frekvence                                             | RateLimitExceeded         | Překročení limitu přenosové rychlosti                                                                                                               |
| kvóta        | Překročení kvóty                                                  | QuotaExceeded             | Překročení kvóty volání. Kvótu se doplnit v xx:xx:xx. - nebo - šířku pásma kvóty. Kvótu se doplnit v xx:xx:xx. |
| jsonp        | Hodnota parametru zpětného volání je neplatný (obsahuje chybné znaky) | CallbackParameterInvalid  | Hodnota parametru zpětného volání {zpětného volání název parametru} není platný identifikátor jazyka JavaScript.                                          |
| Filtr IP    | Nepovedlo se parsovat volající IP z požadavku                          | FailedToParseCallerIP     | Nepovedlo se vytvořit IP adresu pro volajícího. Přístup byl odepřen.                                                                        |
| Filtr IP    | Volající IP není v seznamu povolených aplikací                                | CallerIpNotAllowed        | IP adresy volajícího {ip-address} není povolena. Přístup byl odepřen.                                                                        |
| Filtr IP    | Volající IP je v seznamu blokovaných položek                                    | CallerIpBlocked           | IP adresy volajícího blokovaný. Přístup byl odepřen.                                                                                         |
| check-header | Chybí požadované záhlaví není uvedené nebo hodnota               | HeaderNotFound            | Hlavička {název hlavičky} se nenašla v požadavku. Přístup byl odepřen.                                                                    |
| check-header | Chybí požadované záhlaví není uvedené nebo hodnota               | HeaderValueNotAllowed     | Hodnota hlavičky {název hlavičky} z {hodnota hlavičky} není povolena. Přístup byl odepřen.                                                          |
| validate-jwt | V požadavku chybí Jwt token                                 | TokenNotFound             | Nebyl nalezen v žádosti o token JWT. Přístup byl odepřen.                                                                                         |
| validate-jwt | Nepovedlo se ověřit podpis                                     | TokenSignatureInvalid     | < zprávu z knihovny jwt\>. Přístup byl odepřen.                                                                                          |
| validate-jwt | Neplatná cílová skupina                                                | TokenAudienceNotAllowed   | < zprávu z knihovny jwt\>. Přístup byl odepřen.                                                                                          |
| validate-jwt | Neplatný vydavatel                                                  | TokenIssuerNotAllowed     | < zprávu z knihovny jwt\>. Přístup byl odepřen.                                                                                          |
| validate-jwt | Vypršela platnost tokenu.                                                   | TokenExpired              | < zprávu z knihovny jwt\>. Přístup byl odepřen.                                                                                          |
| validate-jwt | Podpisový klíč se nepodařilo přeložit podle ID                            | TokenSignatureKeyNotFound | < zprávu z knihovny jwt\>. Přístup byl odepřen.                                                                                          |
| validate-jwt | Chybí požadované deklarace identit z tokenu                          | TokenClaimNotFound        | JWT token neobsahuje následující deklarace: < c1\>, < c2\>,... Přístup byl odepřen.                                                            |
| validate-jwt | Neshoda hodnot deklarací identity                                           | TokenClaimValueNotAllowed | Hodnota deklarace identity {název deklarace} z {hodnota deklarace} není povolena. Přístup byl odepřen.                                                             |
| validate-jwt | Jiné chyby ověření                                       | JwtInvalid                | < zprávu z knihovny tokenů jwt\>                                                                                                          |

## <a name="example"></a>Příklad:

Nastavení zásad rozhraní API:

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

a odesílání neoprávněného požadavku bude mít za následek následující odpověď:

![Chyba Neautorizováno odpovědi](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Další postup

Práce se zásadami pro další informace najdete v tématu:

+ [Zásady ve službě API Management](api-management-howto-policies.md)
+ [Transformujte rozhraní API](transform-api.md)
+ [Referenční příručce o zásadách](api-management-policy-reference.md) úplný seznam zásad příkazy a jejich nastavení
+ [Ukázky zásad](policy-samples.md)   