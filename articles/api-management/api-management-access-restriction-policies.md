---
title: Zásady omezení přístupu služby Azure API Management | Microsoft Docs
description: Další informace o omezení zásady přístupu, které jsou k dispozici pro použití v Azure API Management.
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
ms.openlocfilehash: 1b6aea5152e9eb5152b400d74d834e31eb883458
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110217"
---
# <a name="api-management-access-restriction-policies"></a>Zásady omezení přístupu služby API Management
Toto téma obsahuje odkaz pro následující zásady služby API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady ve službě API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="AccessRestrictionPolicies"></a> Zásady omezení přístupu  
  
-   [Kontrola HTTP záhlaví](api-management-access-restriction-policies.md#CheckHTTPHeader) -vynucuje existence nebo hodnoty hlavičky protokolu HTTP.  
-   [Omezení četnosti volání podle předplatného](api-management-access-restriction-policies.md#LimitCallRate) -špičky využití brání rozhraní API omezením četnosti volání, na základě za předplatné.  
-   [Omezení četnosti volání podle klíče](#LimitCallRateByKey) -špičky využití brání rozhraní API omezením četnosti volání, na základě na klíč.  
-   [Omezit volající IP adresy](api-management-access-restriction-policies.md#RestrictCallerIPs) -filtry (umožňuje nebo zakazuje) volání z konkrétní IP adresy a rozsahy adres.  
-   [Nastavení kvóty využití podle předplatného](api-management-access-restriction-policies.md#SetUsageQuota) -umožňuje vynucovat obnovitelných nebo doba života volání svazku nebo šířky pásma kvótu, na základě za předplatné.  
-   [Nastavení kvóty využití podle klíče](#SetUsageQuotaByKey) -umožňuje vynucovat obnovitelných nebo doba života volání svazku nebo šířky pásma kvótu, na základě na klíč.  
-   [Ověření JWT](api-management-access-restriction-policies.md#ValidateJWT) -vynucuje existence a platnosti token JWT extrahovány ze zadaného záhlaví HTTP nebo parametr zadaný dotaz.  
  
##  <a name="CheckHTTPHeader"></a> Zkontrolujte hlavičky protokolu HTTP  
 Použití `check-header` zásady vynutit, aby požadavek má zadanou hlavičku HTTP. Volitelně můžete zkontrolovat zda záhlaví obsahuje konkrétní hodnotu nebo zkontrolujte rozsah povolených hodnot. Pokud kontrola selže, zásady ukončí zpracování žádosti a vrátí stav kód a chybová zpráva HTTP určena v zásadách.  
  
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
|Kontrola – hlavička|Kořenový element.|Ano|  
|hodnota|Povolená hodnota hlavičky protokolu HTTP. Víc elementů hodnota zadaná, kontroly považuje úspěšné, pokud některá z hodnot shody.|Ne|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|se nezdařila zkontrolujte chybových zpráv|Chybová zpráva pro vrátit v textu odpovědi HTTP, pokud hlavička neexistuje nebo má neplatnou hodnotu. Tuto zprávu musí mít žádné speciální znaky správně řídicí sekvencí.|Ano|neuvedeno|  
|failed-check-httpcode|Kód stavu HTTP vrátit, pokud hlavička neexistuje nebo má neplatnou hodnotu.|Ano|neuvedeno|  
|název hlavičky|Název záhlaví HTTP ke kontrole.|Ano|neuvedeno|  
|Ignorovat případu|Lze nastavit na hodnotu True nebo False. Pokud je nastaven na hodnotu True případ se ignoruje, pokud hodnota hlavičky se porovná sadu přijatelných hodnot.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásady:** vstupní, výstupní  
  
-   **Zásady obory:** globální, produktu, rozhraní API, operace  
  
##  <a name="LimitCallRate"></a> Omezení četnosti volání podle předplatného  
 `rate-limit` Zásada brání API nárazovým zvýšením požadavků na základě předplatného za omezením četnosti volání na zadaný počet za zadané časové období. Když se aktivuje tuto zásadu volající obdrží `429 Too Many Requests` stavový kód odpovědi.  
  
> [!IMPORTANT]
>  Tuto zásadu lze použít jenom jednou za zásady dokumentu.  
>   
>  [Výrazy zásad](api-management-policy-expressions.md) nelze použít v některém z atributy zásad pro tuto zásadu.  
  
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
|rozhraní api|Přidáte jeden nebo víc z těchto prvků do volání míru omezení na rozhraní API v rámci produktu. Produkt a rozhraní API volat rychlost, kterou omezení platí nezávisle. Rozhraní API může být buď přes odkazovaný `name` nebo `id`. Pokud oba atributy jsou k dispozici, `id` se použije a `name` budou ignorovány.|Ne|  
|operace|Přidáte jeden nebo víc z těchto elementů k omezení na rychlost volání operace v rámci rozhraní API. Produktu, rozhraní API a operace četnosti, kterou omezení platí nezávisle. Operaci lze odkazovat buď prostřednictvím `name` nebo `id`. Pokud oba atributy jsou k dispozici, `id` se použije a `name` budou ignorovány.|Ne|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|jméno|Název rozhraní API, pro které chcete použít omezení četnosti.|Ano|neuvedeno|  
|volání|Maximální celkový počet volání během zadaného časového intervalu v povoleno `renewal-period`.|Ano|neuvedeno|  
|období obnovení|Doba v sekundách, po které se kvóta resetuje.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásady:** příchozí  
  
-   **Zásady obory:** produktu  
  
##  <a name="LimitCallRateByKey"></a> Omezení četnosti volání podle klíče  
 `rate-limit-by-key` Zásada brání API nárazovým zvýšením požadavků na základě za klíč omezením četnosti volání na zadaný počet za zadané časové období. Klíč může mít hodnotu libovolný řetězec a se většinou poskytuje pomocí výrazů zásad. Volitelné Přírůstek podmínku můžete přidat k určení, které požadavky mělo být započítáno směrem limit. Když se aktivuje tuto zásadu volající obdrží `429 Too Many Requests` stavový kód odpovědi.  
  
 Další informace a příklady těchto zásad najdete v tématu [pokročilé omezování požadavků pomocí Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).  
  
> [!IMPORTANT]
>  Tuto zásadu lze použít jenom jednou za zásady dokumentu.  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<rate-limit-by-key calls="number"  
                   renewal-period="seconds"   
                   increment-condition="condition"   
                   counter-key="key value" />  
  
```  
  
### <a name="example"></a>Příklad:  
 V následujícím příkladu omezení četnosti se ukládá do klíčů pomocí IP adresy volajícího.  
  
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
|volání|Maximální celkový počet volání během zadaného časového intervalu v povoleno `renewal-period`.|Ano|neuvedeno|  
|kontrolní klíč|Klíč pro zásady omezení četnosti.|Ano|neuvedeno|  
|Increment – podmínky|Logický výraz, který zadáte, pokud požadavek by měl počítat směrem kvótu (`true`).|Ne|neuvedeno|  
|období obnovení|Doba v sekundách, po které se kvóta resetuje.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásady:** příchozí  
  
-   **Zásady obory:** globální, produktu, rozhraní API, operace  
  
##  <a name="RestrictCallerIPs"></a> Omezit volající IP adresy  
 `ip-filter` Zásad filtry (umožňuje nebo zakazuje) volání z konkrétní IP adresy a rozsahy adres.  
  
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
|Adresa|Určuje jednu IP adresu, podle kterého chcete filtrovat.|Alespoň jeden `address` nebo `address-range` prvek je nutný.|  
|rozsah adres z = "address" k = "address"|Určuje rozsah IP adres, podle kterého chcete filtrovat.|Alespoň jeden `address` nebo `address-range` prvek je nutný.|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|rozsah adres z = "address" k = "address"|Rozsah IP adres Pokud chcete povolit nebo odepřít přístup.|Požadováno, pokud `address-range` element se používá.|neuvedeno|  
|Filtr IP akce = "Povolit &#124; nezakazuje"|Určuje, zda má být volání povoleno, nebo není pro zadané IP adresy a rozsahy.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásady:** příchozí  
-   **Zásady obory:** globální, produktu, rozhraní API, operace  
  
##  <a name="SetUsageQuota"></a> Nastavení kvóty využití podle předplatného  
 `quota` Zásady vynucují obnovitelných nebo doba života volání svazku nebo šířky pásma kvótu, na základě za předplatné.  
  
> [!IMPORTANT]
>  Tuto zásadu lze použít jenom jednou za zásady dokumentu.  
>   
>  [Výrazy zásad](api-management-policy-expressions.md) nelze použít v některém z atributy zásad pro tuto zásadu.  
  
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
|rozhraní api|Přidáte jeden nebo víc z těchto elementů zavést kvóty volání na rozhraní API v rámci produktu. Produkt a kvóty volání rozhraní API platí nezávisle. Rozhraní API může být buď přes odkazovaný `name` nebo `id`. Pokud oba atributy jsou k dispozici, `id` se použije a `name` budou ignorovány.|Ne|  
|operace|Přidáte jeden nebo víc z těchto elementů zavést kvóty volání na operace v rámci rozhraní API. Kvóty volání produktu, rozhraní API a operace platí nezávisle. Operaci lze odkazovat buď prostřednictvím `name` nebo `id`. Pokud oba atributy jsou k dispozici, `id` se použije a `name` budou ignorovány.|Ne|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|jméno|Název rozhraní API nebo operaci, pro kterou platí kvótu.|Ano|neuvedeno|  
|Šířka pásma|Maximální počet kilobajtů během zadaného časového intervalu v povoleno `renewal-period`.|Buď `calls`, `bandwidth`, nebo společně musí být zadány oba.|neuvedeno|  
|volání|Maximální celkový počet volání během zadaného časového intervalu v povoleno `renewal-period`.|Buď `calls`, `bandwidth`, nebo společně musí být zadány oba.|neuvedeno|  
|období obnovení|Doba v sekundách, po které se kvóta resetuje.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásady:** příchozí  
-   **Zásady obory:** produktu  
  
##  <a name="SetUsageQuotaByKey"></a> Nastavení kvóty využití podle klíče  
 `quota-by-key` Zásady vynucují obnovitelných nebo doba života volání svazku nebo šířky pásma kvótu, na základě na klíč. Klíč může mít hodnotu libovolný řetězec a se většinou poskytuje pomocí výrazů zásad. Volitelné Přírůstek podmínku můžete přidat k určení, které požadavky mělo být započítáno směrem kvótu.  
  
 Další informace a příklady těchto zásad najdete v tématu [pokročilé omezování požadavků pomocí Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).  
  
> [!IMPORTANT]
>  Tuto zásadu lze použít jenom jednou za zásady dokumentu.  
>   
>  [Výrazy zásad](api-management-policy-expressions.md) nelze použít v některém z atributy zásad pro tuto zásadu.  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<quota-by-key calls="number"   
              bandwidth="kilobytes"   
              renewal-period="seconds"  
              increment-condition="condition"   
              counter-key="key value" />  
  
```  
  
### <a name="example"></a>Příklad:  
 V následujícím příkladu kvóta se ukládá do klíčů pomocí IP adresy volajícího.  
  
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
|Šířka pásma|Maximální počet kilobajtů během zadaného časového intervalu v povoleno `renewal-period`.|Buď `calls`, `bandwidth`, nebo společně musí být zadány oba.|neuvedeno|  
|volání|Maximální celkový počet volání během zadaného časového intervalu v povoleno `renewal-period`.|Buď `calls`, `bandwidth`, nebo společně musí být zadány oba.|neuvedeno|  
|kontrolní klíč|Klíč pro zásady kvót.|Ano|neuvedeno|  
|Increment – podmínky|Logický výraz, který zadáte, pokud požadavek by měl počítat směrem kvótu (`true`)|Ne|neuvedeno|  
|období obnovení|Doba v sekundách, po které se kvóta resetuje.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásady:** příchozí  
-   **Zásady obory:** globální, produktu, rozhraní API, operace  
  
##  <a name="ValidateJWT"></a> Ověřit token JWT  
 `validate-jwt` Zásady vynucují existence a platnosti token JWT extrahovat z buď zadané hlavičky protokolu HTTP nebo parametr zadaný dotaz.  
  
> [!IMPORTANT]
>  `validate-jwt` Zásad vyžaduje, aby `exp` registrované deklarace identity je součástí JWT token, pokud `require-expiration-time` atribut je zadán a nastavený na `false`.  
> `validate-jwt` Zásad podporuje HS256 a RS256 podpisový algoritmy. Pro HS256 klíč je třeba zadat vložené v rámci zásady v podobě kódováním base64. RS256 klíč má zajistit prostřednictvím koncového bodu Open ID konfigurace.  
  
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
  
#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Autorizace přístupu k operacím na základě tokenu deklarací  
 Tento příklad ukazuje způsob použití [ověření JWT](api-management-access-restriction-policies.md#ValidateJWT) u tokenu deklarací na základě zásad předem autorizace přístupu k operacím. Ukázka konfiguraci a použití této zásady, najdete v části [cloudu zahrnují díl 177: rozhraní API funkce správy více s Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) a rychlé převíjení vpřed na 13:50. Rychlé převinutí vpřed do 15:00 v tématu Zásady nakonfigurované v editoru zásad a potom do 18:50 pro předvedení volání operace z portálu pro vývojáře s i bez požadované autorizační token.  
  
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
|ověřit token jwt|Kořenový element.|Ano|  
|cílové skupiny|Obsahuje seznam deklarací identity přijatelné cílové skupiny, které mohou existovat na tokenu. Pokud nejsou více hodnot cílové skupiny, pak se pokus o každé hodnotě dokud všechny jsou vyčerpány (v takovém případě ověření selže) nebo dokud jeden neuspěje. Je třeba zadat alespoň jednu cílovou skupinu.|Ne|  
|Vystavitel podpisového klíče|Seznam kódováním Base64 zabezpečení klíčů používaných k ověření podepsané tokeny. Pokud nejsou více zabezpečení klíčů, pak se pokus o každý klíč dokud všechny jsou vyčerpány (v takovém případě ověření selže) nebo dokud jeden neuspěje (je to užitečné pro výměnu tokenů). Mezi klíčové prvky mít volitelný `id` atribut použitý k porovnání `kid` deklarací identity.|Ne|  
|vystavitele|Seznam přijatelné objektů, které vydán token. Pokud jsou přítomny více hodnot vystavitele, pak se pokus o každé hodnotě dokud všechny jsou vyčerpány (v takovém případě ověření selže) nebo dokud jeden neuspěje.|Ne|  
|Konfigurace openid|Element použit k určení kompatibilní endpoint konfigurace Open ID, ze kterého lze získat podpisového klíče a vystavitele.|Ne|  
|požadované deklarace identity|Obsahuje seznam deklarací identity nacházet na tokenu mohla být považovány za platné očekávání. Když `match` je atribut nastaven na `all` každá hodnota deklarace identity v zásady musí být v tokenu pro ověření úspěšné. Když `match` je atribut nastaven na `any` nejméně jedna deklarace identity musí být v tokenu pro ověření úspěšné.|Ne|  
|záhlaví zumo hlavního klíče|Hlavní klíč pro tokeny vydané službou Azure Mobile Services|Ne|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|hodiny zkosení|Časový interval. Slouží k určení očekávané maximální časový rozdíl mezi systémovými hodinami vydavatel tokenu a instanci služby API Management.|Ne|0 sekund|  
|se nezdařilo ověření chybových zpráv|Chybová zpráva pro vrátí v textu odpovědi HTTP, pokud položka JWT neprojde ověřením. Tuto zprávu musí mít žádné speciální znaky správně řídicí sekvencí.|Ne|Výchozí chybovou zprávu, závisí na ověření problém, například "JWT nejsou k dispozici."|  
|se nezdařilo httpcode ověření|Kód stavu HTTP vrátit, pokud není položka JWT projít ověřením.|Ne|401|  
|název hlavičky|Název záhlaví HTTP, která uchovává token.|Buď `header-name` nebo `query-parameter-name` musí být zadaný; ale ne pomocí obou.|neuvedeno|  
|id|`id` Atributu u `key` element umožňuje zadejte řetězec, který bude odpovídat proti `kid` deklarací identity v tokenu (pokud existuje) a zjistěte, příslušný klíč k ověření podpisu.|Ne|neuvedeno|  
|shoda|`match` Atributu u `claim` element určuje, zda každá hodnota deklarace identity v zásady musí být přítomen v tokenu pro ověření úspěšné. Možné hodnoty:<br /><br /> -                          `all` -Každá hodnota deklarace identity v zásady musí být v tokenu pro ověření úspěšné.<br /><br /> -                          `any` -Hodnota nejméně jedna deklarace identity musí být přítomen v tokenu pro ověření úspěšné.|Ne|all|  
|Název dotazu paremeter|Název parametr dotazu tokenem.|Buď `header-name` nebo `query-paremeter-name` musí být zadaný; ale ne pomocí obou.|neuvedeno|  
|požadovat čas vypršení platnosti|Logická hodnota. Určuje, zda deklaraci identity vypršení platnosti je vyžadováno v tokenu.|Ne|true (pravda)|
|require-scheme|Název tokenu scheme, například "Nosiče". Když tento atribut nastavený, zásady zajistí, že zadané schéma je k dispozici v Hodnota hlavičky ověřování.|Ne|neuvedeno|
|Vyžadovat podepsané tokeny|Logická hodnota. Určuje, jestli je potřeba být podepsaný token.|Ne|true (pravda)|  
|Oddělovač|Řetězec. Určuje oddělovače (například ",") má být použit pro extrahování sadu hodnot z více hodnot deklarací identity.|Ne|neuvedeno| 
|url|Otevřete adresu URL koncového bodu ID konfigurace ze které lze získat metadata Open ID konfigurace. Odpověď by měla odpovídat specifikací definovaným na adrese URL:`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`.  Pro Azure Active Directory použít následující adresu URL: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` nahraďte název vašeho adresáře klienta, například `contoso.onmicrosoft.com`.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásady:** příchozí  
-   **Zásady obory:** globální, produktu, rozhraní API, operace  
  
## <a name="next-steps"></a>Další postup

Práce se zásadami pro další informace najdete v tématu:

+ [Zásady ve službě API Management](api-management-howto-policies.md)
+ [Transformuje rozhraní API](transform-api.md)
+ [Referenční informace o zásadách](api-management-policy-reference.md) pro úplný seznam příkazy zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)   
