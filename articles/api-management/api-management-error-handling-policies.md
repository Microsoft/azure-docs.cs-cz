---
title: Při zpracování chyb v zásadách správy rozhraní Azure API | Dokumenty společnosti Microsoft
description: Zjistěte, jak reagovat na chybové stavy, ke kterým může dojít během zpracování požadavků ve správě rozhraní Azure API.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902459"
---
# <a name="error-handling-in-api-management-policies"></a>Zpracování chyb v zásadách služby API Management

Poskytnutím `ProxyError` objektu azure api management umožňuje vydavatelům reagovat na chybové stavy, ke kterým může dojít během zpracování požadavků. K `ProxyError` objektu je přistupovat prostřednictvím [kontextu. Vlastnost LastError](api-management-policy-expressions.md#ContextVariables) a může být `on-error` použita zásadami v části zásad. Tento článek obsahuje odkaz na možnosti zpracování chyb ve správě rozhraní AZURE API.

## <a name="error-handling-in-api-management"></a>Zpracování chyb ve správě rozhraní API

Zásady ve správě rozhraní `inbound` `backend`AZURE `outbound`API `on-error` jsou rozděleny do , , a oddíly, jak je znázorněno v následujícím příkladu.

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

Během zpracování požadavku jsou provedeny předdefinované kroky spolu se všemi zásadami, které jsou v oboru pro požadavek. Pokud dojde k chybě, zpracování okamžitě `on-error` přejde do oddílu zásad.
Oddíl `on-error` zásad lze použít v libovolném oboru. Vydavatelé rozhraní API můžete nakonfigurovat vlastní chování, jako je například protokolování chyby do centra událostí nebo vytvoření nové odpovědi pro návrat k volajícímu.

> [!NOTE]
> Oddíl `on-error` není k dispozici v zásadách ve výchozím nastavení. Chcete-li `on-error` přidat oddíl do zásady, přejděte na požadovanou zásadu v editoru zásad a přidejte ji. Další informace o konfiguraci zásad naleznete [v tématu Zásady ve správě rozhraní API](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).
>
> Pokud neexistuje `on-error` žádný oddíl, volající obdrží 400 nebo 500 zpráv odpovědi HTTP, pokud dojde k chybě.

### <a name="policies-allowed-in-on-error"></a>Zásady povolené při chybě

Následující zásady lze použít `on-error` v části zásady.

-   [Zvolte](api-management-advanced-policies.md#choose)
-   [set-proměnná](api-management-advanced-policies.md#set-variable)
-   [najít a nahradit](api-management-transformation-policies.md#Findandreplacestringinbody)
-   [návratová odpověď](api-management-advanced-policies.md#ReturnResponse)
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)
-   [set-metoda](api-management-advanced-policies.md#SetRequestMethod)
-   [stav nastavení](api-management-advanced-policies.md#SetStatus)
-   [odeslat žádost](api-management-advanced-policies.md#SendRequest)
-   [send-one-way-request](api-management-advanced-policies.md#SendOneWayRequest)
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)
-   [json-xml](api-management-transformation-policies.md#ConvertJSONtoXML)
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)

## <a name="lasterror"></a>LastError

Dojde-li k chybě a ovládací `on-error` prvek přejde do oddílu zásad, chyba je uložena v [kontextu. Vlastnost LastError,](api-management-policy-expressions.md#ContextVariables) ke které lze přistupovat pomocí zásad v `on-error` části. LastError má následující vlastnosti.

| Name (Název)       | Typ   | Popis                                                                                               | Požaduje se |
| ---------- | ------ | --------------------------------------------------------------------------------------------------------- | -------- |
| `Source`   | řetězec | Pojmenuje prvek, kde došlo k chybě. Může se na poruše nebo vestavěném názvu kroku kanálu.      | Ano      |
| `Reason`   | řetězec | Kód chyby vhodný pro stroj, který lze použít při zpracování chyb.                                       | Ne       |
| `Message`  | řetězec | Popis chyby čitelný pro člověka.                                                                         | Ano      |
| `Scope`    | řetězec | Název oboru, kde došlo k chybě a může být jedním z "globální", "produkt", "api" nebo "operace" | Ne       |
| `Section`  | řetězec | Název oddílu, kde došlo k chybě. Možné hodnoty: "příchozí", "back-end", "odchozí" nebo "při chybě".      | Ne       |
| `Path`     | řetězec | Určuje vnořenou zásadu, například "choose[3]/when[2]".                                                 | Ne       |
| `PolicyId` | řetězec | Hodnota atributu, `id` pokud je zadána zákazníkem, na zásadu, kde došlo k chybě             | Ne       |

> [!TIP]
> Stavový kód můžete získat prostřednictvím kontextu. Response.StatusCode.

> [!NOTE]
> Všechny zásady `id` mají volitelný atribut, který lze přidat do kořenového prvku zásady. Pokud tento atribut je k dispozici v zásadě, když dojde k chybě, `context.LastError.PolicyId` hodnotu atributu lze načíst pomocí vlastnosti.

## <a name="predefined-errors-for-built-in-steps"></a>Předdefinované chyby pro předdefinované kroky

Následující chyby jsou předdefinovány pro chybové stavy, ke kterým může dojít během vyhodnocení předdefinovaných kroků zpracování.

| Zdroj        | Podmínka                                 | Důvod                  | Zpráva                                                                                                                |
| ------------- | ----------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| konfigurace | Identifikátor Uri se neshoduje s žádným rozhraním API nebo operací | Operace nebyla nalezena.       | Nelze spárovat příchozí požadavek na operaci.                                                                      |
| autorizace | Klíč předplatného není dodán.             | SubscriptionKeyNotFound | Přístup byl odepřen z důvodu chybějícího klíče předplatného. Nezapomeňte zahrnout klíč předplatného při vytváření požadavků na toto rozhraní API. |
| autorizace | Hodnota klíče předplatného je neplatná.         | SubscriptionKeyNeplatné  | Přístup byl odepřen z důvodu neplatného klíče předplatného. Ujistěte se, že zadáte platný klíč pro aktivní předplatné.            |
| vícenásobné | Následné připojení (z klienta do brány pro správu rozhraní API) bylo přerušeno klientem, zatímco požadavek čeká na vyřízení. | Selhání připojení klienta | vícenásobné |
| vícenásobné | Upstream připojení (z brány správy rozhraní API do back-endové služby) nebyla vytvořena nebo byla přerušena back-endem | BackendConnection Selhání | vícenásobné |
| vícenásobné | Při vyhodnocování určitého výrazu došlo k výjimce za běhu. | VýrazHodnotavyhodnoceníselhání | vícenásobné |

## <a name="predefined-errors-for-policies"></a>Předdefinované chyby pro zásady

Následující chyby jsou předdefinovány pro chybové stavy, ke kterým může dojít během vyhodnocení zásad.

| Zdroj       | Podmínka                                                       | Důvod                    | Zpráva                                                                                                                              |
| ------------ | --------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| limit rychlosti   | Byl překročen limit rychlosti.                                             | RateLimit překročena         | Je překročen limit rychlosti.                                                                                                               |
| kvóta        | Byla překročena kvóta.                                                  | Kvóta byla překročena.             | Překročení kvóty volání. Kvóta bude doplněna v xx:xx:xx. -nebo- Mimo kvótu šířky pásma. Kvóta bude doplněna v xx:xx:xx. |
| Jsonp        | Hodnota parametru zpětného volání je neplatná (obsahuje nesprávné znaky) | Parametr zpětného volání je neplatný.  | Hodnota parametru zpětného volání {callback-parameter-name} není platný identifikátor JavaScriptu.                                          |
| ip-filtr    | Analýza IP adresy volajícího z požadavku se nezdařila.                          | FailedToParseCallerIP     | Nepodařilo se vytvořit adresu IP pro volajícího. Přístup se odepřel.                                                                        |
| ip-filtr    | Ip adresa volajícího není v seznamu povolených.                                | CalleripNotAllowed        | Ip adresa volajícího {ip-address} není povolena. Přístup se odepřel.                                                                        |
| ip-filtr    | Ip adresa volajícího je v seznamu blokovaných.                                    | CallerIpBlocked           | Ip adresa volajícího je blokována. Přístup se odepřel.                                                                                         |
| kontrolní hlavička | Požadovaná hlavička nebyla předložena nebo chybí hodnota.               | HeaderNotFound            | Hlavička {název záhlaví} nebyla v požadavku nalezena. Přístup se odepřel.                                                                    |
| kontrolní hlavička | Požadovaná hlavička nebyla předložena nebo chybí hodnota.               | HeaderValueNotPovoleno     | Hodnota záhlaví {header-name} {header-value} není povolena. Přístup se odepřel.                                                          |
| validate-jwt | Jwt token chybí v požadavku                                 | TokenNotFound             | JWT nebyl nalezen v požadavku. Přístup se odepřel.                                                                                         |
| validate-jwt | Ověření podpisu se nezdařilo.                                     | TokenSignatureNeplatný     | <zprávu z knihovny\>jwt . Přístup se odepřel.                                                                                          |
| validate-jwt | Neplatná cílová skupina                                                | TokenAudienceNotAllowed   | <zprávu z knihovny\>jwt . Přístup se odepřel.                                                                                          |
| validate-jwt | Neplatný vydavatel                                                  | TokenIssuerNotAllowed     | <zprávu z knihovny\>jwt . Přístup se odepřel.                                                                                          |
| validate-jwt | Vypršela platnost tokenu.                                                   | TokenExpired              | <zprávu z knihovny\>jwt . Přístup se odepřel.                                                                                          |
| validate-jwt | Podpisový klíč nebyl vyřešen ID.                            | TokenSignatureKeyNotFound | <zprávu z knihovny\>jwt . Přístup se odepřel.                                                                                          |
| validate-jwt | V tokenu chybí požadované deklarace identity.                          | TokenClaimNotFound        | JWT token chybí následující nároky:\><c1 , <c2\>, ... Přístup se odepřel.                                                            |
| validate-jwt | Neshoda hodnot deklarací                                           | TokenClaimValueNotAllowed | Deklarace {claim-name} hodnota {claim-value} není povolena. Přístup se odepřel.                                                             |
| validate-jwt | Další selhání ověření                                       | JwtNeplatný                | <zpráva z knihovny JWT\>                                                                                                          |
| předání nebo odeslání | Kód stavu odpovědi HTTP a záhlaví nebyla přijata z back-endu v rámci nakonfigurovaného časového času | Časový limit | vícenásobné |

## <a name="example"></a>Příklad

Nastavení zásad rozhraní API na:

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

a odeslání neoprávněné žádosti bude mít za následek následující odpověď:

![Neautorizovaná chybová odpověď](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami naleznete v tématu:

-   [Zásady ve správě rozhraní API](api-management-howto-policies.md)
-   [Transformovat api](transform-api.md)
-   [Odkaz na zásady](api-management-policy-reference.md) pro úplný seznam prohlášení zásad a jejich nastavení
-   [Ukázky zásad](policy-samples.md)
