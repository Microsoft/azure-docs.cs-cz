---
title: Zpracování chyb v zásad Azure API managementu | Microsoft Docs
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
ms.openlocfilehash: 73609e802eceea6aa94d77cef6ca1d654264973d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265003"
---
# <a name="error-handling-in-api-management-policies"></a>Zpracování chyb v zásady služby API Management

Tím, že poskytuje `ProxyError` objektu Azure API Management umožňuje vydavatelů reagovat na chybové stavy, které mohou nastat při zpracování požadavků. `ProxyError` Objekt přistupuje prostřednictvím [kontextu. Poslední chyba](api-management-policy-expressions.md#ContextVariables) vlastnost a mohou být využívána zásad v `on-error` zásad. Tento článek obsahuje odkaz pro Chyba funkce zpracování v Azure API Management.  
  
## <a name="error-handling-in-api-management"></a>Zpracování chyb v API Management

 Zásady ve službě Azure API Management jsou rozdělené do `inbound`, `backend`, `outbound`, a `on-error` částech, jak je znázorněno v následujícím příkladu.  
  
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
  
Během zpracování požadavku, provádění předdefinované kroků spolu se všechny zásady, které se nacházejí v oboru pro daný požadavek. Pokud dojde k chybě, zpracování okamžitě přejde `on-error` zásad.  
`on-error` Zásad můžete použít na jakémkoli oboru. Vydavatelé rozhraní API můžete nakonfigurovat vlastní chování, například protokolování chyba do centra událostí nebo vytvořit novou odpověď má být vrácena volajícímu.  
  
> [!NOTE]
>  `on-error` Části není k dispozici v zásadách ve výchozím nastavení. Chcete-li přidat `on-error` části k zásadě, přejděte do požadované zásady v editoru zásad a přidejte ji. Další informace o konfiguraci zásad najdete v tématu [zásady ve službě API Management](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).  
>   
>  Pokud není žádná `on-error` části volající bude přijímat zprávy odpovědi HTTP 400 nebo 500, pokud dojde k chybě.  
  
### <a name="policies-allowed-in-on-error"></a>Zásady povolené na chybu

 Tyto zásady mohou být používány `on-error` zásad.  
  
-   [Zvolte](api-management-advanced-policies.md#choose)  
-   [nastavená proměnná](api-management-advanced-policies.md#set-variable)  
-   [hledání a nahrazování](api-management-transformation-policies.md#Findandreplacestringinbody)  
-   [vrátí odpověď](api-management-advanced-policies.md#ReturnResponse)  
-   [set – hlavička](api-management-transformation-policies.md#SetHTTPheader)  
-   [set – metoda](api-management-advanced-policies.md#SetRequestMethod)  
-   [Nastavit stav](api-management-advanced-policies.md#SetStatus)  
-   [odeslání žádosti](api-management-advanced-policies.md#SendRequest)  
-   [odeslání jeden způsob požadavků](api-management-advanced-policies.md#SendOneWayRequest)  
-   [protokol eventhub](api-management-advanced-policies.md#log-to-eventhub)  
-   [JSON do xml](api-management-transformation-policies.md#ConvertJSONtoXML)  
-   [XML – json](api-management-transformation-policies.md#ConvertXMLtoJSON)  
  
## <a name="lasterror"></a>LastError

 Když dojde k chybě a řízení přejde `on-error` část zásad, chyba je uložen v [kontextu. Poslední chyba](api-management-policy-expressions.md#ContextVariables) vlastnosti, která je přístupná pomocí zásad v `on-error` oddílu. Poslední chyba má následující vlastnosti.  
  
| Název     | Typ   | Popis                                                                                               | Požaduje se |
|----------|--------|-----------------------------------------------------------------------------------------------------------|----------|
| Zdroj   | řetězec | Názvy elementu, kde došlo k chybě. Může být zásady nebo název kroku předdefinované kanálu.     | Ano      |
| Důvod   | řetězec | Kód chyby popisný počítače, které by mohly být použity v zpracování chyb.                                       | Ne       |
| Zpráva  | řetězec | Popis chyby čitelná pro člověka.                                                                         | Ano      |
| Rozsah    | řetězec | Název oboru, kde chybovou zprávu a může mít jednu z "globální", "produkt", "api" nebo "operace" | Ne       |
| Sekce  | řetězec | Název oddílu, kde došlo k chybě. Možné hodnoty: "příchozí", "back-end", "odchozí" nebo "na chyba".       | Ne       |
| Cesta     | řetězec | Určuje vnořené zásad, například "[3] zvolte / při [2]".                                                        | Ne       |
| PolicyId | řetězec | Hodnota `id` atribut, pokud zadaný zákazník na zásadu, kde došlo k chybě             | Ne       |

> [!TIP]
> Stavový kód můžete přistupovat prostřednictvím kontextu. Response.StatusCode.  

> [!NOTE]
> Všechny zásady mít volitelný `id` atribut, který jde přidat do kořenového elementu zásad. Pokud se tento atribut nachází v zásadách, když dojde k chybě, můžete ji načíst hodnotu atributu pomocí `context.LastError.PolicyId` vlastnost.

## <a name="predefined-errors-for-built-in-steps"></a>Předdefinované chyby pro vestavěné kroky  
 Tyto chyby jsou předdefinovány pro chybové podmínky, které se můžou vyskytnout během vyhodnocování předdefinované zpracování kroky.  
  
| Zdroj        | Podmínka                                 | Důvod                  | Zpráva                                                                                                                |
|---------------|-------------------------------------------|-------------------------|------------------------------------------------------------------------------------------------------------------------|
| konfigurace | Identifikátor URI neodpovídá žádné rozhraní API nebo operace | OperationNotFound       | Nelze spárovat příchozího požadavku pro operaci.                                                                      |
| Autorizace | Není zadaný klíč předplatného             | SubscriptionKeyNotFound | Přístup byl odepřen z důvodu chybějícího klíč předplatného. Ujistěte se, že při zasílání požadavků na toto rozhraní API obsahovat klíč předplatného. |
| Autorizace | Hodnota klíče předplatného je neplatná.         | SubscriptionKeyInvalid  | Přístup byl odepřen z důvodu neplatný odběr klíč. Ujistěte se, že zadejte platný klíč pro aktivní předplatné.            |
  
## <a name="predefined-errors-for-policies"></a>Předdefinované chyby pro zásady  
 Tyto chyby jsou předdefinovány pro chybové podmínky, které se můžou vyskytnout během hodnocení zásad.  
  
| Zdroj       | Podmínka                                                       | Důvod                    | Zpráva                                                                                                                              |
|--------------|-----------------------------------------------------------------|---------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| omezení četnosti   | Byl překročen limit rychlost                                             | RateLimitExceeded         | Překročení omezení četnosti                                                                                                               |
| kvóta        | Kvóta byla překročena.                                                  | QuotaExceeded             | Překročení kvóty volání. Kvóta bude doplnit v xx:xx:xx. - nebo - Out šířky pásma kvóty. Kvóta bude doplnit v xx:xx:xx. |
| JSONP        | Hodnota parametru zpětného volání je neplatný (obsahuje nesprávné znaky) | CallbackParameterInvalid  | Hodnota parametru zpětného volání {zpětného volání název parametru} není platný identifikátor jazyka JavaScript.                                          |
| Filtr IP    | Nepodařilo se analyzovat volající IP z požadavku                          | FailedToParseCallerIP     | Nepodařilo se navázat IP adresu pro volajícího. Přístup byl odepřen.                                                                        |
| Filtr IP    | Volající IP není v seznamu povolených aplikací                                | CallerIpNotAllowed        | Volající IP adresa {adresa} není povolena. Přístup byl odepřen.                                                                        |
| Filtr IP    | Volající IP je v seznamu blokovaných položek                                    | CallerIpBlocked           | Zablokování volající IP adresy. Přístup byl odepřen.                                                                                         |
| Kontrola – hlavička | Chybí požadovaná hlavička nebyla uvedené nebo hodnota               | HeaderNotFound            | Hlavička {název hlavičky} nebyla nalezena v požadavku. Přístup byl odepřen.                                                                    |
| Kontrola – hlavička | Chybí požadovaná hlavička nebyla uvedené nebo hodnota               | HeaderValueNotAllowed     | Hodnota hlavičky {název hlavičky} {hodnoty hlavičky} není povolená. Přístup byl odepřen.                                                          |
| ověřit token jwt | V požadavku chybí Jwt token                                 | TokenNotFound             | Nebyl nalezen v žádosti o token JWT. Přístup byl odepřen.                                                                                         |
| ověřit token jwt | Nepovedlo se ověřit podpis                                     | TokenSignatureInvalid     | < zprávy z knihovny jwt\>. Přístup byl odepřen.                                                                                          |
| ověřit token jwt | Cílová skupina je neplatná                                                | TokenAudienceNotAllowed   | < zprávy z knihovny jwt\>. Přístup byl odepřen.                                                                                          |
| ověřit token jwt | Neplatný vystavitele                                                  | TokenIssuerNotAllowed     | < zprávy z knihovny jwt\>. Přístup byl odepřen.                                                                                          |
| ověřit token jwt | Platnost tokenu                                                   | TokenExpired              | < zprávy z knihovny jwt\>. Přístup byl odepřen.                                                                                          |
| ověřit token jwt | Klíč podpisu nebyla rozpoznána podle ID                            | TokenSignatureKeyNotFound | < zprávy z knihovny jwt\>. Přístup byl odepřen.                                                                                          |
| ověřit token jwt | Chybí požadovaná deklarací z tokenu                          | TokenClaimNotFound        | JWT token chybí následující deklarace identity: < c1\>, < c2\>,... Přístup byl odepřen.                                                            |
| ověřit token jwt | Neshoda hodnoty deklarace identity                                           | TokenClaimValueNotAllowed | Hodnota deklarace identity {deklarace identity name} {hodnota deklarace} není povolená. Přístup byl odepřen.                                                             |
| ověřit token jwt | Jiné chyby ověření                                       | JwtInvalid                | < zprávy z knihovny jwt\>                                                                                                          |

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

![Neoprávněné chybové odpovědi](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Další postup

Práce se zásadami pro další informace najdete v tématu:

+ [Zásady ve službě API Management](api-management-howto-policies.md)
+ [Transformuje rozhraní API](transform-api.md)
+ [Referenční informace o zásadách](api-management-policy-reference.md) pro úplný seznam příkazy zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)   