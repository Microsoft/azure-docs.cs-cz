---
title: Zásady omezení přístupu služby Azure API Management | Dokumentace Microsoftu
description: Další informace o zásady omezení přístupu k dispozici pro použití ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 39f23cd0b0b6081d8e54524a0eedc9cce1fd4571
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55493416"
---
# <a name="api-management-access-restriction-policies"></a>Zásady omezení přístupu služby API Management
Toto téma obsahuje odkaz pro následující zásady služby API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady ve službě API Management](https://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="AccessRestrictionPolicies"></a> Zásady omezení přístupu  
  
-   [Kontrola protokolu HTTP hlavičce](api-management-access-restriction-policies.md#CheckHTTPHeader) -vynucuje existence a/nebo hodnotu hlavičky protokolu HTTP.  
-   [Omezení četnosti volání podle předplatného](api-management-access-restriction-policies.md#LimitCallRate) -špičky využití brání rozhraní API omezením četnosti volání podle předplatného.  
-   [Omezení četnosti volání podle klíče](#LimitCallRateByKey) -špičky využití brání rozhraní API omezením četnosti volání, na základě za klíč.  
-   [Omezení IP adresy volajícího](api-management-access-restriction-policies.md#RestrictCallerIPs) – filtry (umožňuje nebo zakazuje) volání z konkrétních IP adres a rozsahy adres.  
-   [Nastavení kvóty využití podle předplatného](api-management-access-restriction-policies.md#SetUsageQuota) – slouží k vynucování obnovitelných nebo doba života volání svazku a/nebo šířku pásma kvóty, na základě předplatného.  
-   [Nastavení kvóty využití podle klíče](#SetUsageQuotaByKey) – slouží k vynucování obnovitelných nebo doba života volání svazku a/nebo šířku pásma kvóty, na základě za klíč.  
-   [Ověřit token JWT](api-management-access-restriction-policies.md#ValidateJWT) -vynucuje existenci a platnost extrahují z zadanou hlavičku protokolu HTTP nebo parametr dotazu zadaný token JWT.  
  
##  <a name="CheckHTTPHeader"></a> Zkontrolujte hlavičky protokolu HTTP  
 Použití `check-header` zásadu vynucující, žádost má zadanou hlavičku protokolu HTTP. Volitelně můžete zkontrolovat, zjistěte, jestli mají záhlaví konkrétní hodnotu nebo vyhledat rozsah povolených hodnot. Pokud kontrola selže, zásady ukončí zpracování požadavku a vrátí stav kódu a chybové zprávy HTTP určené zásady.  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">  
    <value>Value1</value>  
    <value>Value2</value>  
</check-header>  
```  
  
### <a name="example"></a>Příklad:  
  
```xml  
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">  
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>  
</check-header>  
```  
  
### <a name="elements"></a>Elementy  
  
|Název|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|check-header|Kořenový element.|Ano|  
|hodnota|Povolená hodnota záhlaví HTTP. Pokud je zadaných více elementů hodnotu, kontroly se považuje za úspěchu, pokud některou z hodnot je nalezena shoda.|Ne|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|failed-check-error-message|Chybová zpráva má vrátit v těle odpovědi protokolu HTTP, pokud záhlaví neexistuje nebo má neplatnou hodnotu. Tato zpráva musí mít žádné speciální znaky správně uvozeny zpětným lomítkem.|Ano|neuvedeno|  
|failed-check-httpcode|Kód stavu HTTP vrátit Pokud záhlaví neexistuje nebo má neplatnou hodnotu.|Ano|neuvedeno|  
|název hlavičky|Název hlavičky protokolu HTTP ke kontrole.|Ano|neuvedeno|  
|ignorování případu|Lze nastavit na hodnotu True nebo False. Pokud je nastaveno na True případ je ignorována, pokud hodnota hlavičky se porovná sadu přijatelných hodnot.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásad:** příchozí, odchozí  
  
-   **Zásady obory:** globální, produktu, rozhraní API, operace  
  
##  <a name="LimitCallRate"></a> Omezení četnosti volání podle předplatného  
 `rate-limit` Zásada brání tím, že omezíte počet volání na zadané číslo za zadané časové období špičky využití rozhraní API na základě předplatného. Jakmile se tato zásada se aktivuje volající přijímat `429 Too Many Requests` stavového kódu odpovědi.  
  
> [!IMPORTANT]
>  Tuto zásadu lze použít pouze jednou za dokument zásad.  
>   
>  [Výrazy zásad](api-management-policy-expressions.md) nelze použít v některém z atributů zásad pro tyto zásady.  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<rate-limit calls="number" renewal-period="seconds">  
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />  
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />  
    </api>  
</rate-limit>  
```  
  
### <a name="example"></a>Příklad:  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <rate-limit calls="20" renewal-period="90" />  
    </inbound>  
    <outbound>  
        <base />          
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementy  
  
|Název|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|Nastavte limit|Kořenový element.|Ano|  
|rozhraní api|Přidejte jednu nebo více z těchto prvků a stanovit omezení frekvence volání rozhraní API v rámci produktu. Produkt a rozhraní API volat rychlost, kterou omezení platí nezávisle na sobě. Rozhraní API může být buď prostřednictvím `name` nebo `id`. Pokud nejsou zadány oba atributy, `id` se použije a `name` budou ignorovány.|Ne|  
|operace|Přidejte jeden nebo více z těchto elementů pro omezení frekvence volání na operace v rámci rozhraní API. Produktu, rozhraní API a operace četnosti, kterou omezení platí nezávisle na sobě. Operace může být buď prostřednictvím `name` nebo `id`. Pokud nejsou zadány oba atributy, `id` se použije a `name` budou ignorovány.|Ne|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|jméno|Název rozhraní API, které chcete použít omezení četnosti.|Ano|neuvedeno|  
|volání|Maximální počet povolených během zadaného časového intervalu ve volání `renewal-period`.|Ano|neuvedeno|  
|období obnovení|Doba v sekundách, po jejichž uplynutí se kvóta resetuje.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásad:** příchozí  
  
-   **Zásady obory:** produktu  
  
##  <a name="LimitCallRateByKey"></a> Omezení četnosti volání podle klíče  

> [!IMPORTANT]
> Tato funkce je k dispozici v **spotřeby** úroveň služby API Management.

 `rate-limit-by-key` Zásada zabraňuje špičky využití rozhraní API na základě klíče za tím, že omezíte počet volání na zadané číslo za zadané časové období. Klíč může obsahovat libovolné řetězcovou hodnotu a se většinou poskytuje pomocí výrazů zásad. Volitelný krok podmínku lze přidat k určení, které požadavky by měla být započítává limit. Jakmile se tato zásada se aktivuje volající přijímat `429 Too Many Requests` stavového kódu odpovědi.  
  
 Další informace a příklady těchto zásad najdete v tématu [pokročilé omezování požadavků pomocí Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<rate-limit-by-key calls="number"  
                   renewal-period="seconds"   
                   increment-condition="condition"   
                   counter-key="key value" />  
  
```  
  
### <a name="example"></a>Příklad:  
 V následujícím příkladu je omezení četnosti označenými pomocí IP adresy volajícího.  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <rate-limit-by-key  calls="10"  
              renewal-period="60"  
              increment-condition="@(context.Response.StatusCode == 200)"  
              counter-key="@(context.Request.IpAddress)"/>  
    </inbound>  
    <outbound>  
        <base />          
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementy  
  
|Název|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|Nastavte limit|Kořenový element.|Ano|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|volání|Maximální počet povolených během zadaného časového intervalu ve volání `renewal-period`.|Ano|neuvedeno|  
|čítače klíč|Klíč pro zásady omezení četnosti.|Ano|neuvedeno|  
|increment-condition|Logický výraz, který zadáte, pokud požadavek by měl započítává kvóty (`true`).|Ne|neuvedeno|  
|období obnovení|Doba v sekundách, po jejichž uplynutí se kvóta resetuje.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásad:** příchozí  
  
-   **Zásady obory:** globální, produktu, rozhraní API, operace  
  
##  <a name="RestrictCallerIPs"></a> Omezení IP adresy volajícího  
 `ip-filter` Zásad filtry (umožňuje nebo zakazuje) volání z konkrétních IP adres a rozsahy adres.  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<ip-filter action="allow | forbid">  
    <address>address</address>  
    <address-range from="address" to="address" />  
</ip-filter>  
```  
  
### <a name="example"></a>Příklad:  
  
```xml  
<ip-filter action="allow | forbid">  
    <address>address</address>  
    <address-range from="address" to="address" />  
</ip-filter>  
```  
  
### <a name="elements"></a>Elementy  
  
|Název|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|Filtr IP|Kořenový element.|Ano|  
|Adresa|Určuje jednu IP adresu, podle kterého chcete filtrovat.|Alespoň jeden `address` nebo `address-range` je vyžadován element.|  
|rozsah adres z = "address" k "address" =|Určuje rozsah IP adres, podle kterého chcete filtrovat.|Alespoň jeden `address` nebo `address-range` je vyžadován element.|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|rozsah adres z = "address" k "address" =|Rozsah IP adres umožňuje povolit nebo odepřít přístup.|Požadováno, pokud `address-range` element se používá.|neuvedeno|  
|Akce filtru IP = "Povolit &#124; zakázat"|Určuje, zda mají být povolená volání nebo není pro zadané IP adresy a rozsahy adres.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásad:** příchozí  
-   **Zásady obory:** globální, produktu, rozhraní API, operace  
  
##  <a name="SetUsageQuota"></a> Nastavení kvóty využití podle předplatného  
 `quota` Zásada vynucuje obnovitelných nebo doba života volání svazku a/nebo šířku pásma kvóty, na základě předplatného.  
  
> [!IMPORTANT]
>  Tuto zásadu lze použít pouze jednou za dokument zásad.  
>   
>  [Výrazy zásad](api-management-policy-expressions.md) nelze použít v některém z atributů zásad pro tyto zásady.  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">  
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />  
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />  
    </api>  
</quota>  
```  
  
### <a name="example"></a>Příklad:  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementy  
  
|Název|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|kvóta|Kořenový element.|Ano|  
|rozhraní api|Přidejte jednu nebo více z těchto prvků a stanovit kvótu volání rozhraní API v rámci produktu. Produkt a kvóty volání rozhraní API se uplatňují nezávisle na sobě. Rozhraní API může být buď prostřednictvím `name` nebo `id`. Pokud nejsou zadány oba atributy, `id` se použije a `name` budou ignorovány.|Ne|  
|operace|Přidejte jednu nebo více z těchto prvků a stanovit kvótu volání na operace v rámci rozhraní API. Nezávisle na sobě se uplatňují kvóty pro volání produktu, rozhraní API a operace. Operace může být buď prostřednictvím `name` nebo `id`. Pokud nejsou zadány oba atributy, `id` se použije a `name` budou ignorovány.|Ne|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|jméno|Název rozhraní API nebo operace, pro kterou se vztahují kvóty.|Ano|neuvedeno|  
|Šířka pásma|Maximální počet povolených během zadaného časového intervalu v kilobajtech `renewal-period`.|Buď `calls`, `bandwidth`, nebo společně musí být zadány oba.|neuvedeno|  
|volání|Maximální počet povolených během zadaného časového intervalu ve volání `renewal-period`.|Buď `calls`, `bandwidth`, nebo společně musí být zadány oba.|neuvedeno|  
|období obnovení|Doba v sekundách, po jejichž uplynutí se kvóta resetuje.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásad:** příchozí  
-   **Zásady obory:** produktu  
  
##  <a name="SetUsageQuotaByKey"></a> Nastavení kvóty využití podle klíče  

> [!IMPORTANT]
> Tato funkce je k dispozici v **spotřeby** úroveň služby API Management.

 `quota-by-key` Zásada vynucuje obnovitelných nebo doba života volání svazku a/nebo šířku pásma kvóty, na základě za klíč. Klíč může obsahovat libovolné řetězcovou hodnotu a se většinou poskytuje pomocí výrazů zásad. Volitelný krok podmínku lze přidat k určení, které požadavky by měla být započítává kvóty. Pokud více zásad by zvýšit stejnou hodnotu klíče, je pouze jednou zvýší na požadavek. Při dosažení limitu volání volající obdrží `403 Forbidden` stavového kódu odpovědi.
  
 Další informace a příklady těchto zásad najdete v tématu [pokročilé omezování požadavků pomocí Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).  
  
>  [Výrazy zásad](api-management-policy-expressions.md) nelze použít v některém z atributů zásad pro tyto zásady.  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<quota-by-key calls="number"   
              bandwidth="kilobytes"   
              renewal-period="seconds"  
              increment-condition="condition"   
              counter-key="key value" />  
  
```  
  
### <a name="example"></a>Příklad:  
 V následujícím příkladu kvóty je nastaven podle IP adresy volajícího.  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"  
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"  
                      counter-key="@(context.Request.IpAddress)" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementy  
  
|Název|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|kvóta|Kořenový element.|Ano|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|Šířka pásma|Maximální počet povolených během zadaného časového intervalu v kilobajtech `renewal-period`.|Buď `calls`, `bandwidth`, nebo společně musí být zadány oba.|neuvedeno|  
|volání|Maximální počet povolených během zadaného časového intervalu ve volání `renewal-period`.|Buď `calls`, `bandwidth`, nebo společně musí být zadány oba.|neuvedeno|  
|čítače klíč|Klíč pro zásady kvót.|Ano|neuvedeno|  
|increment-condition|Logický výraz, který zadáte, pokud požadavek by měl započítává kvóty (`true`)|Ne|neuvedeno|  
|období obnovení|Doba v sekundách, po jejichž uplynutí se kvóta resetuje.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásad:** příchozí  
-   **Zásady obory:** globální, produktu, rozhraní API, operace  
  
##  <a name="ValidateJWT"></a> Ověřit token JWT  
 `validate-jwt` Zásada vynucuje existence a platnost token JWT extrahovat z buď zadanou hlavičku protokolu HTTP nebo parametr zadaný dotaz.
  
> [!IMPORTANT]
>  `validate-jwt` Zásad vyžaduje, aby `exp` registrované deklarací identity je součástí JWT token, není-li `require-expiration-time` je určen atribut a je nastavena na `false`.  
> `validate-jwt` Zásady podporuje HS256 a RS256 podpisové algoritmy. U HS256 musí klíč poskytuje vložené v rámci zásady v podobě kódování base64. Klíč pro RS256 musí poskytovat prostřednictvím Open ID konfigurace koncového bodu.
> `validate-jwt` Zásady podporuje tokeny šifrován pomocí následující šifrovací algoritmy A128CBC-HS256 A192CBC-HS384 A256CBC HS512 symetrické klíče.
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<validate-jwt   
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"   
    failed-validation-httpcode="http status code to return on failure"   
    failed-validation-error-message="error message to return on failure"   
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"   
    clock-skew="allowed clock skew in seconds">  
  <issuer-signing-keys>  
    <key>base64 encoded signing key</key>  
    <!-- if there are multiple keys, then add additional key elements -->  
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>  
    <!-- if there are multiple keys, then add additional key elements -->  
  </decryption-keys>
  <audiences>  
    <audience>audience string</audience>  
    <!-- if there are multiple possible audiences, then add additional audience elements -->  
  </audiences>  
  <issuers>  
    <issuer>issuer string</issuer>  
    <!-- if there are multiple possible issuers, then add additional issuer elements -->  
  </issuers>  
  <required-claims>  
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>  
      <!-- if there is more than one allowed values, then add additional value elements -->  
    </claim>  
    <!-- if there are multiple possible allowed values, then add additional value elements -->  
  </required-claims>  
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />  
  <zumo-master-key id="key identifier">key value</zumo-master-key>  
</validate-jwt>  
  
```  
  
### <a name="examples"></a>Příklady  
  
#### <a name="azure-mobile-services-token-validation"></a>Ověření tokenu Azure Mobile Services  
  
```xml  
<validate-jwt header-name="x-zumo-auth" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Supplied access token is invalid.">  
    <issuers>  
        <issuer>urn:microsoft:windows-azure:zumo</issuer>  
    </issuers>  
    <audiences>  
        <audience>Facebook</audience>  
    </audiences>  
    <issuer-signing-keys>  
        <zumo-master-key id="0">insert key here</zumo-master-key>  
    </issuer-signing-keys>  
</validate-jwt>  
```  
  
#### <a name="azure-active-directory-token-validation"></a>Ověření tokenu Azure Active Directory  
  
```xml  
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">  
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />  
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>  
        <claim name="id" match="all">  
            <value>insert claim here</value>  
        </claim>  
    </required-claims>  
</validate-jwt>  
```  

  
#### <a name="azure-active-directory-b2c-token-validation"></a>Ověření tokenu Azure Active Directory B2C  
  
```xml  
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">  
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>  
        <claim name="id" match="all">  
            <value>insert claim here</value>  
        </claim>  
    </required-claims>  
</validate-jwt>  
```  
  
#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Povolit přístup k operacím na základě tokenu deklarací identity  
 Tento příklad ukazuje způsob použití [ověření JWT](api-management-access-restriction-policies.md#ValidateJWT) zásad má předběžně autorizovat přístup k operacím na základě tokenu deklarací identity. Ukázka konfigurace a používání této zásady, najdete v části [Cloud Cover epizodě 177: Další funkce API Management s Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) a přechod na libovolný krok 13:50. Rychlé převinutí vpřed na 15:00, jaké zásady nakonfigurované v editoru zásad a potom na 18:50 ukázku volání operace z portálu pro vývojáře s i bez požadovaný autorizační token.  
  
```xml  
<!-- Copy the following snippet into the inbound section at the api (or higher) level to pre-authorize access to operations based on token claims -->  
<set-variable name="signingKey" value="insert signing key here" />  
<choose>  
  <when condition="@(context.Request.Method.Equals("patch",StringComparison.OrdinalIgnoreCase))">  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
      <required-claims>  
        <claim name="edit">  
          <value>true</value>  
        </claim>  
      </required-claims>  
    </validate-jwt>  
  </when>  
  <when condition="@(new [] {"post", "put"}.Contains(context.Request.Method,StringComparer.OrdinalIgnoreCase))">  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
      <required-claims>  
        <claim name="create">  
          <value>true</value>  
        </claim>  
      </required-claims>  
    </validate-jwt>  
  </when>  
  <otherwise>  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
    </validate-jwt>  
  </otherwise>  
</choose>  
```  
  
### <a name="elements"></a>Elementy  
  
|Element|Popis|Požaduje se|  
|-------------|-----------------|--------------|  
|validate-jwt|Kořenový element.|Ano|  
|cílové skupiny|Obsahuje seznam deklarací přijatelné cílové skupiny, které může být k dispozici na tokenu. Pokud jsou k dispozici více hodnot cílovou skupinu, pak každá hodnota se pokusila dokud buď všechny jsou vyčerpány (v takovém případě ověření selže), nebo dokud některý krok neuspěje. Je třeba zadat alespoň jednu cílovou skupinu.|Ne|  
|issuer-signing-keys|Seznam klíčů s kódováním Base64 zabezpečení slouží k ověření podepsané tokeny. Pokud jsou k dispozici víc klíčů zabezpečení, pak je každý klíč pokusili dokud buď všechny jsou vyčerpány (v takovém případě ověření selže), nebo dokud jeden neuspěje (režim vhodný pro token výměny). Klíčové prvky mít volitelně `id` atribut používaný k porovnání s `kid` deklarací identity.|Ne|  
|dešifrovací klíče|Seznam s kódováním Base64 klíče používané k dešifrování tokenů. Pokud je víc klíčů zabezpečení, každý klíč se pokusila až do vyčerpání buď všechny klíče (v takovém případě ověření selže), nebo dokud nebude úspěšné klíč. Klíčové prvky mít volitelně `id` atribut používaný k porovnání s `kid` deklarací identity.|Ne|  
|Vystavitelé|Seznam objektů přijatelný, která token vydala. Pokud více hodnoty vystavitelů jsou k dispozici, pak každá hodnota se pokusila dokud buď všechny jsou vyčerpány (v takovém případě ověření selže), nebo dokud některý krok neuspěje.|Ne|  
|openid-config|Prvek použit k určení kompatibilní Open ID konfigurace koncový bod ze kterého můžete získat podpisové klíče a vydavatele.|Ne|  
|požadované deklarace identity|Obsahuje seznam deklarací, aby byly v tokenu, aby se považují za platná. Když `match` atribut je nastaven na `all` každá hodnota deklarace identity v zásadách musí být k dispozici v tokenu pro ověření úspěšné. Když `match` atribut je nastaven na `any` nejméně jedna deklarace identity musí být k dispozici v tokenu pro ověření úspěšné.|Ne|  
|záhlaví zumo-master-key|Hlavní klíč pro tokeny vystavené službou Azure Mobile Services|Ne|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|časový posun|Časový interval. Slouží k zadání očekávané maximální časový rozdíl mezi systémovými hodinami vydavatel tokenu a instance API Management.|Ne|0 sekund|  
|se nezdařilo ověření – chybové zprávy|Chybová zpráva pro vrátí v těle odpovědi protokolu HTTP, pokud token JWT nesplňuje podmínky ověření. Tato zpráva musí mít žádné speciální znaky správně uvozeny zpětným lomítkem.|Ne|Výchozí chybovou zprávu, závisí na problém s ověřováním, například "token JWT není k dispozici."|  
|failed-validation-httpcode|Kód stavu HTTP k vrácení, pokud není položka JWT projít ověřením.|Ne|401|  
|název hlavičky|Název hlavičky protokolu HTTP s tokenem.|Buď `header-name` nebo `query-parameter-name` musí být zadaný; ale ne obě možnosti současně.|neuvedeno|  
|id|`id` Atribut na `key` prvek můžete zadat řetězec, který bude hledána `kid` deklarací identity v tokenu (pokud existuje) a zjistěte, příslušný klíč pro ověření podpisu.|Ne|neuvedeno|  
|shoda|`match` Atribut na `claim` element určuje, zda každá hodnota deklarace identity v zásadách musí být k dispozici v tokenu pro ověření úspěšné. Možné hodnoty:<br /><br /> -                          `all` -Každá hodnota deklarace identity v zásadách musí být k dispozici v tokenu pro ověření úspěšné.<br /><br /> -                          `any` – alespoň jednu deklaraci identity hodnota musí být k dispozici v tokenu pro ověření úspěšné.|Ne|all|  
|query-parameter-name|Název parametru dotazu tokenem.|Buď `header-name` nebo `query-parameter-name` musí být zadaný; ale ne obě možnosti současně.|neuvedeno|  
|vyžadovat čas vypršení platnosti|Datový typ Boolean. Určuje, jestli se vyžaduje deklaraci identity vypršení platnosti tokenu.|Ne|true (pravda)|
|require-scheme|Název tokenu schéma, například "Nosiče". Pokud tento atribut je nastaven, zásady zajistí, že zadané schéma je k dispozici v Hodnota hlavičky autorizace.|Ne|neuvedeno|
|požadovat podepsané tokenů|Datový typ Boolean. Určuje, zda je token musí být podepsán.|Ne|true (pravda)|  
|Oddělovač|řetězec. Určuje oddělovač (například ",") má být použit pro extrahování sadu hodnot z více Vážíme si toho deklarace identity.|Ne|neuvedeno| 
|url|Otevřete adresu URL koncového bodu konfigurace ID kde lze získat metadata Open ID konfigurace. Odpověď by měla být podle specifikace definované na adrese URL:`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`.  Azure Active Directory použít následující adresu URL: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` nahraďte název tenanta adresáře, třeba `contoso.onmicrosoft.com`.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásad:** příchozí  
-   **Zásady obory:** globální, produktu, rozhraní API, operace  
  
## <a name="next-steps"></a>Další postup

Práce se zásadami pro další informace najdete v tématu:

+ [Zásady ve službě API Management](api-management-howto-policies.md)
+ [Transformujte rozhraní API](transform-api.md)
+ [Referenční příručce o zásadách](api-management-policy-reference.md) úplný seznam zásad příkazy a jejich nastavení
+ [Ukázky zásad](policy-samples.md)   
