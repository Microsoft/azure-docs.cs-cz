---
title: Zásady omezení přístupu pro správu rozhraní Azure API | Dokumenty společnosti Microsoft
description: Přečtěte si o zásadách omezení přístupu, které jsou k dispozici pro použití ve správě rozhraní Azure API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 3ba620d66b84e6724751b2024059e8ecd66888cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266114"
---
# <a name="api-management-access-restriction-policies"></a>Zásady omezení přístupu ke službě API Management

Toto téma obsahuje odkaz na následující zásady správy rozhraní API. Informace o přidávání a konfiguraci zásad naleznete [v tématu Zásady ve správě rozhraní API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="access-restriction-policies"></a><a name="AccessRestrictionPolicies"></a>Zásady omezení přístupu

-   [Kontrola hlavičky HTTP](api-management-access-restriction-policies.md#CheckHTTPHeader) – vynucuje existenci nebo hodnotu hlavičky HTTP.
-   [Omezit rychlost volání podle předplatného](api-management-access-restriction-policies.md#LimitCallRate) – zabrání špičkám využití rozhraní API omezením rychlosti volání na základě předplatného.
-   [Omezit rychlost volání podle klíče](#LimitCallRateByKey) – zabrání špičkám využití rozhraní API omezením rychlosti volání na základě klíče.
-   [Omezit ip adresy volajícího](api-management-access-restriction-policies.md#RestrictCallerIPs) – filtry (povolí/odepře) volání z konkrétních IP adres a/nebo rozsahů adres.
-   [Nastavení kvóty využití podle předplatného](api-management-access-restriction-policies.md#SetUsageQuota) – umožňuje vynutit obnovitelný nebo dlouhodobý objem volání nebo kvótu šířky pásma na základě předplatného.
-   [Nastavení kvóty využití podle klíče](#SetUsageQuotaByKey) – umožňuje vynutit obnovitelný nebo dlouhodobý objem volání nebo kvótu šířky pásma na základě klíče.
-   [Ověřit JWT](api-management-access-restriction-policies.md#ValidateJWT) - Vynucuje existenci a platnost JWT extrahované z zadané hlavičky HTTP nebo zadaný parametr dotazu.

> [!TIP]
> Zásady omezení přístupu můžete použít v různých oborech pro různé účely. Můžete například zabezpečit celé rozhraní API pomocí ověřování `validate-jwt` AAD použitím zásad na úrovni rozhraní API `claims` nebo ji můžete použít na úrovni operace rozhraní API a použít pro podrobnější řízení.

## <a name="check-http-header"></a><a name="CheckHTTPHeader"></a>Kontrola hlavičky PROTOKOLU HTTP

Pomocí `check-header` zásady vynuťte, že požadavek má zadanou hlavičku HTTP. Volitelně můžete zkontrolovat, zda má záhlaví určitou hodnotu, nebo zkontrolovat rozsah povolených hodnot. Pokud kontrola selže, zásada ukončí zpracování požadavků a vrátí stavový kód HTTP a chybovou zprávu určenou zásadou.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">
    <value>Value1</value>
    <value>Value2</value>
</check-header>
```

### <a name="example"></a>Příklad

```xml
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>
</check-header>
```

### <a name="elements"></a>Elementy

| Name (Název)         | Popis                                                                                                                                   | Požaduje se |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| kontrolní hlavička | Kořenový prvek.                                                                                                                                 | Ano      |
| value        | Povolená hodnota hlavičky HTTP. Pokud je zadáno více prvků hodnoty, kontrola je považována za úspěšnou, pokud se některá z hodnot shoduje. | Ne       |

### <a name="attributes"></a>Atributy

| Name (Název)                       | Popis                                                                                                                                                            | Požaduje se | Výchozí |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| zpráva o chybě se nezdařilo | Pokud záhlaví neexistuje nebo má neplatnou hodnotu, vrátí se v textu odpovědi HTTP chybová zpráva. Tato zpráva musí mít všechny speciální znaky správně uvozeny. | Ano      | Není dostupné.     |
| kód http-failed-check-httpcode      | Stavový kód HTTP, který chcete vrátit, pokud záhlaví neexistuje nebo má neplatnou hodnotu.                                                                                        | Ano      | Není dostupné.     |
| název záhlaví                | Název hlavičky HTTP ke kontrole.                                                                                                                                  | Ano      | Není dostupné.     |
| ignorovat-case                | Může být nastavena na True nebo False. Pokud je nastavena na True case je ignorována při porovnání hodnoty záhlaví proti sadě přijatelných hodnot.                                    | Ano      | Není dostupné.     |

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí, odchozí

-   **Obory zásad:** všechny obory

## <a name="limit-call-rate-by-subscription"></a><a name="LimitCallRate"></a>Omezit sazbu volání podle předplatného

Zásady `rate-limit` zabraňuje špičky využití rozhraní API na základě předplatného omezením rychlost volání na zadané číslo za zadané časové období. Při spuštění této zásady volající `429 Too Many Requests` obdrží kód stavu odpovědi.

> [!IMPORTANT]
> Tuto zásadu lze použít pouze jednou pro dokument zásady.
>
> [Výrazy zásad](api-management-policy-expressions.md) nelze použít v žádném z atributů zásad pro tuto zásadu.

> [!CAUTION]
> Vzhledem k distribuované povaze škrcení architektury omezení omezení rychlosti není nikdy zcela přesné. Rozdíl mezi nakonfigurovaným a reálným počtem povolených požadavků se liší v závislosti na objemu požadavku a rychlosti, latenci back-endu a dalších faktorech.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</rate-limit>
```

### <a name="example"></a>Příklad

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

| Name (Název)       | Popis                                                                                                                                                                                                                                                                                              | Požaduje se |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| limit rychlosti | Kořenový prvek.                                                                                                                                                                                                                                                                                            | Ano      |
| rozhraní api        | Přidejte jeden nebo více těchto prvků, abyste v rámci produktu zavedli limit rychlosti volání na rozhraní API. Limity rychlosti volání produktu a rozhraní API se uplatňují nezávisle. API lze odkazovat `name` buď `id`přes nebo . Pokud jsou k dispozici `id` oba atributy, budou použity a `name` budou ignorovány.                    | Ne       |
| Operace  | Přidejte jeden nebo více těchto prvků, abyste uvalili limit rychlosti volání pro operace v rámci rozhraní API. Limity rychlosti volání produktu, rozhraní API a operace se používají nezávisle. Na provoz lze odkazovat buď přes `name` nebo `id`. Pokud jsou k dispozici `id` oba atributy, budou použity a `name` budou ignorovány. | Ne       |

### <a name="attributes"></a>Atributy

| Name (Název)           | Popis                                                                                           | Požaduje se | Výchozí |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| jméno           | Název rozhraní API, pro které chcete použít limit rychlosti.                                                | Ano      | Není dostupné.     |
| Volání          | Maximální celkový počet volání povolených během časového `renewal-period`intervalu určeného v . | Ano      | Není dostupné.     |
| období obnovení | Časové období v sekundách, po kterém se kvóta resetuje.                                              | Ano      | Není dostupné.     |

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí

-   **Obory zásad:** produkt, api, provoz

## <a name="limit-call-rate-by-key"></a><a name="LimitCallRateByKey"></a>Omezit rychlost volání klíčem

> [!IMPORTANT]
> Tato funkce není k dispozici ve **správě spotřebovávek** rozhraní API.

Zásada `rate-limit-by-key` zabraňuje špičkám využití rozhraní API na základě klíče omezením rychlosti volání na zadané číslo za zadané časové období. Klíč může mít libovolnou řetězcovou hodnotu a je obvykle k dispozici pomocí výrazu zásady. Volitelná podmínka přírůstku může být přidána k určení, které požadavky by měly být započítány do limitu. Při spuštění této zásady volající `429 Too Many Requests` obdrží kód stavu odpovědi.

Další informace a příklady této zásady najdete [v tématu Upřesnit omezení požadavků pomocí azure api managementu](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

> [!CAUTION]
> Vzhledem k distribuované povaze škrcení architektury omezení omezení rychlosti není nikdy zcela přesné. Rozdíl mezi nakonfigurovaným a reálným počtem povolených požadavků se liší v závislosti na objemu požadavku a rychlosti, latenci back-endu a dalších faktorech.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" />

```

### <a name="example"></a>Příklad

V následujícím příkladu je limit rychlosti zakódován IP adresou volajícího.

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

| Name (Název)              | Popis   | Požaduje se |
| ----------------- | ------------- | -------- |
| sazba-limit-by-key | Kořenový prvek. | Ano      |

### <a name="attributes"></a>Atributy

| Name (Název)                | Popis                                                                                           | Požaduje se | Výchozí |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| Volání               | Maximální celkový počet volání povolených během časového `renewal-period`intervalu určeného v . | Ano      | Není dostupné.     |
| počítadlo         | Klíč, který chcete použít pro zásady limitu sazby.                                                             | Ano      | Není dostupné.     |
| přírůstek-stav | Logický výraz určující, zda má být požadavek započítán do kvóty (`true`).        | Ne       | Není dostupné.     |
| období obnovení      | Časové období v sekundách, po kterém se kvóta resetuje.                                              | Ano      | Není dostupné.     |

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí

-   **Obory zásad:** všechny obory

## <a name="restrict-caller-ips"></a><a name="RestrictCallerIPs"></a>Omezit ip adresy volajícího

`ip-filter` Filtry zásad (povoluje/zakazuje) volání z konkrétních ADRES IP nebo rozsahů adres.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Příklad

V následujícím příkladu zásada povoluje pouze požadavky přicházející buď z jedné IP adresy, nebo z rozsahu zadaných IP adres.

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Elementy

| Name (Název)                                      | Popis                                         | Požaduje se                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-filtr                                 | Kořenový prvek.                                       | Ano                                                            |
| adresa                                   | Určuje jednu adresu IP, na kterou se má filtrovat.   | Je vyžadován `address` `address-range` alespoň jeden prvek nebo prvek. |
| address-range from="address" to="address" | Určuje rozsah adresy IP, na které se má filtrovat. | Je vyžadován `address` `address-range` alespoň jeden prvek nebo prvek. |

### <a name="attributes"></a>Atributy

| Name (Název)                                      | Popis                                                                                 | Požaduje se                                           | Výchozí |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| address-range from="address" to="address" | Rozsah adres IP, pro které je třeba povolit nebo odepřít přístup.                                        | Povinné při `address-range` použití prvku. | Není dostupné.     |
| ip-filter action="povolit &#124; zakázat"    | Určuje, zda mají být volání povolena nebo ne pro zadané adresy IP a rozsahy. | Ano                                                | Není dostupné.     |

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí
-   **Obory zásad:** všechny obory

## <a name="set-usage-quota-by-subscription"></a><a name="SetUsageQuota"></a>Nastavení kvóty využití podle předplatného

Zásady `quota` vynucují obnovitelný nebo celoživotní objem volání nebo kvótu šířky pásma na základě předplatného.

> [!IMPORTANT]
> Tuto zásadu lze použít pouze jednou pro dokument zásady.
>
> [Výrazy zásad](api-management-policy-expressions.md) nelze použít v žádném z atributů zásad pro tuto zásadu.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</quota>
```

### <a name="example"></a>Příklad

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

| Name (Název)      | Popis                                                                                                                                                                                                                                                                                  | Požaduje se |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| kvóta     | Kořenový prvek.                                                                                                                                                                                                                                                                                | Ano      |
| rozhraní api       | Přidejte jeden nebo více těchto prvků, chcete-li uložit kvótu volání na rozhraní API v rámci produktu. Kvóty volání produktu a rozhraní API se používají nezávisle. API lze odkazovat `name` buď `id`přes nebo . Pokud jsou k dispozici `id` oba atributy, budou použity a `name` budou ignorovány.                    | Ne       |
| Operace | Přidejte jeden nebo více těchto prvků k uložení kvóty volání na operace v rámci rozhraní API. Kvóty volání produktu, rozhraní API a operace se používají nezávisle. Na provoz lze odkazovat buď přes `name` nebo `id`. Pokud jsou k dispozici `id` oba atributy, budou použity a `name` budou ignorovány. | Ne       |

### <a name="attributes"></a>Atributy

| Name (Název)           | Popis                                                                                               | Požaduje se                                                         | Výchozí |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| jméno           | Název rozhraní API nebo operace, pro které se kvóta vztahuje.                                             | Ano                                                              | Není dostupné.     |
| Šířky pásma      | Maximální celkový počet kilobajtů povolený během časového `renewal-period`intervalu určeného v . | Musí `calls` `bandwidth`být zadány buď , nebo oba společně. | Není dostupné.     |
| Volání          | Maximální celkový počet volání povolených během časového `renewal-period`intervalu určeného v .     | Musí `calls` `bandwidth`být zadány buď , nebo oba společně. | Není dostupné.     |
| období obnovení | Časové období v sekundách, po kterém se kvóta resetuje.                                                  | Ano                                                              | Není dostupné.     |

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí
-   **Obory zásad:** produkt

## <a name="set-usage-quota-by-key"></a><a name="SetUsageQuotaByKey"></a>Nastavení kvóty využití podle klíče

> [!IMPORTANT]
> Tato funkce není k dispozici ve **správě spotřebovávek** rozhraní API.

Zásady `quota-by-key` vynucují obnovitelný nebo celoživotní objem volání nebo kvótu šířky pásma na základě klíče. Klíč může mít libovolnou řetězcovou hodnotu a je obvykle k dispozici pomocí výrazu zásady. Volitelná podmínka přírůstku může být přidána, aby bylo možné určit, které požadavky by měly být započítány do kvóty. Pokud by více zásad zvýší stejnou hodnotu klíče, zvýší se pouze jednou na požadavek. Po dosažení limitu volání volající obdrží `403 Forbidden` stavový kód odpovědi.

Další informace a příklady této zásady najdete [v tématu Upřesnit omezení požadavků pomocí azure api managementu](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Příklad

V následujícím příkladu je kvóta zaklíčována IP adresou volajícího.

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

| Name (Název)  | Popis   | Požaduje se |
| ----- | ------------- | -------- |
| kvóta | Kořenový prvek. | Ano      |

### <a name="attributes"></a>Atributy

| Name (Název)                | Popis                                                                                               | Požaduje se                                                         | Výchozí |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| Šířky pásma           | Maximální celkový počet kilobajtů povolený během časového `renewal-period`intervalu určeného v . | Musí `calls` `bandwidth`být zadány buď , nebo oba společně. | Není dostupné.     |
| Volání               | Maximální celkový počet volání povolených během časového `renewal-period`intervalu určeného v .     | Musí `calls` `bandwidth`být zadány buď , nebo oba společně. | Není dostupné.     |
| počítadlo         | Klíč, který chcete použít pro zásady kvóty.                                                                      | Ano                                                              | Není dostupné.     |
| přírůstek-stav | Logický výraz určující, zda má být požadavek započítán do kvóty (`true`)             | Ne                                                               | Není dostupné.     |
| období obnovení      | Časové období v sekundách, po kterém se kvóta resetuje.                                                  | Ano                                                              | Není dostupné.     |

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí
-   **Obory zásad:** všechny obory

## <a name="validate-jwt"></a><a name="ValidateJWT"></a>Ověřit JWT

Zásada `validate-jwt` vynucuje existenci a platnost JWT extrahované z zadané hlavičky HTTP nebo zadaného parametru dotazu.

> [!IMPORTANT]
> Zásada `validate-jwt` vyžaduje, `exp` aby registrovaná deklarace byla zahrnuta do tokenu JWT, pokud `require-expiration-time` není zadán atribut a nastaven na `false`.
> Zásady `validate-jwt` podporují algoritmy podepisování HS256 a RS256. Pro HS256 klíč musí být zadejte vložená v rámci zásady v base64 kódované formuláře. Pro RS256 musí být klíč poskytnut prostřednictvím koncového bodu konfigurace Open ID.
> Zásady `validate-jwt` podporují tokeny šifrované symetrickými klíči pomocí následujících šifrovacích algoritmů A128CBC-HS256, A192CBC-HS384, A256CBC-HS512.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
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
</validate-jwt>

```

### <a name="examples"></a>Příklady

#### <a name="simple-token-validation"></a>Jednoduché ověření tokenu

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="azure-active-directory-token-validation"></a>Ověření tokenu služby Azure Active Directory

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

#### <a name="azure-active-directory-b2c-token-validation"></a>Ověření tokenu Služby Azure Active Directory B2C

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

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Autorizace přístupu k operacím na základě deklarací tokenů

Tento příklad ukazuje, jak použít [zásadu Ověření JWT](api-management-access-restriction-policies.md#ValidateJWT) k autorizaci přístupu k operacím na základě hodnoty deklarací tokenu.

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

### <a name="elements"></a>Elementy

| Element             | Popis                                                                                                                                                                                                                                                                                                                                           | Požaduje se |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-jwt        | Kořenový prvek.                                                                                                                                                                                                                                                                                                                                         | Ano      |
| Publikum           | Obsahuje seznam přijatelných deklarací publika, které mohou být k dispozici na tokenu. Pokud je k dispozici více hodnot publika, je každá hodnota vyzkoušena, dokud nejsou vyčerpány všechny (v takovém případě se ověření nezdaří) nebo dokud jedna neuspěje. Musí být zadána alespoň jedna cílová skupina.                                                                     | Ne       |
| podpisové klíče emitenta | Seznam klíčů zabezpečení kódovaných base64, které slouží k ověření podepsaných tokenů. Pokud je k dispozici více klíčů zabezpečení, je každý klíč vyzkoušen, dokud nejsou vyčerpány všechny (v takovém případě se ověření nezdaří) nebo dokud jeden neuspěje (užitečné pro přechod tokenu). Klíčové prvky `id` mají volitelný atribut, který se používá k porovnání s deklarací. `kid`               | Ne       |
| dešifrování-klíče     | Seznam klíčů kódovaných Base64, které slouží k dešifrování tokenů. Pokud je k dispozici více klíčů zabezpečení, je každý klíč vyzkoušen, dokud nejsou vyčerpány všechny klíče (v takovém případě se ověření nezdaří) nebo dokud nebude klíč úspěšný. Klíčové prvky `id` mají volitelný atribut, který se používá k porovnání s deklarací. `kid`                                                 | Ne       |
| Emitentů             | Seznam přijatelné objekty zabezpečení, které vydaltoken. Pokud je k dispozici více hodnot vystavittele, pak je každá hodnota vyzkoušena, dokud nejsou vyčerpány všechny (v takovém případě se ověření nezdaří) nebo dokud jedna neuspěje.                                                                                                                                         | Ne       |
| openid-config       | Prvek používaný pro určení kompatibilního koncového bodu konfigurace Open ID, ze kterého lze získat podpisové klíče a vystavitela.                                                                                                                                                                                                                        | Ne       |
| požadované pohledávky     | Obsahuje seznam deklarací, u kterých se očekává, že budou přítomny na tokenu, aby byl považován za platný. Pokud `match` je atribut `all` nastaven na každou hodnotu deklarace v zásadě, musí být přítomen v tokenu, aby bylo ověření úspěšné. Pokud `match` je atribut `any` nastaven alespoň jeden deklarace musí být k dispozici v tokenu pro ověření úspěšné. | Ne       |

### <a name="attributes"></a>Atributy

| Name (Název)                            | Popis                                                                                                                                                                                                                                                                                                                                                                                                                                            | Požaduje se                                                                         | Výchozí                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| zkosení hodin                      | Timespan. Slouží k určení maximálního očekávaného časového rozdílu mezi systémovými hodinami vystavittele tokenu a instancí Api Management.                                                                                                                                                                                                                                                                                                               | Ne                                                                               | 0 sekund                                                                         |
| zpráva se selháním ověření a chybou | Chybová zpráva vrátit v těle odpovědi HTTP, pokud JWT neprojde ověření. Tato zpráva musí mít všechny speciální znaky správně uvozeny.                                                                                                                                                                                                                                                                                                 | Ne                                                                               | Výchozí chybová zpráva závisí na problému ověření, například "JWT není k dispozici." |
| kód http-validation-failed      | Stavový kód HTTP, který se má vrátit, pokud JWT neprojde ověřením.                                                                                                                                                                                                                                                                                                                                                                                         | Ne                                                                               | 401                                                                               |
| název záhlaví                     | Název hlavičky HTTP, která obsahuje token.                                                                                                                                                                                                                                                                                                                                                                                                         | Jeden `header-name`z `query-parameter-name` `token-value` , nebo musí být zadán. | Není dostupné.                                                                               |
| název parametru dotazu            | Název parametru dotazu, který obsahuje token.                                                                                                                                                                                                                                                                                                                                                                                                     | Jeden `header-name`z `query-parameter-name` `token-value` , nebo musí být zadán. | Není dostupné.                                                                               |
| hodnota tokenu                     | Výraz vracející řetězec obsahující token JWT                                                                                                                                                                                                                                                                                                                                                                                                     | Jeden `header-name`z `query-parameter-name` `token-value` , nebo musí být zadán. | Není dostupné.                                                                               |
| id                              | Atribut `id` na `key` prvek umožňuje zadat řetězec, který bude `kid` porovnán proti deklaraci v tokenu (pokud je k dispozici) zjistit příslušný klíč pro ověření podpisu.                                                                                                                                                                                                                                           | Ne                                                                               | Není dostupné.                                                                               |
| match                           | Atribut `match` na `claim` prvek určuje, zda každá hodnota deklarace v zásadě musí být k dispozici v tokenu pro ověření úspěšné. Možné hodnoty:<br /><br /> - `all`- každá hodnota deklarace v zásadách musí být k dispozici v tokenu pro ověření úspěšné.<br /><br /> - `any`- alespoň jedna hodnota deklarace musí být k dispozici v tokenu pro ověření úspěšné.                                                       | Ne                                                                               | Vše                                                                               |
| vyžadovat čas vypršení platnosti         | Boolean. Určuje, zda je v tokenu vyžadována deklarace platnosti.                                                                                                                                                                                                                                                                                                                                                                               | Ne                                                                               | true                                                                              |
| systém vyžadovat                  | Název schématu tokenu, např. Pokud je tento atribut nastaven, zásada zajistí, že zadané schéma je k dispozici v hodnotě hlavičky Autorizace.                                                                                                                                                                                                                                                                                    | Ne                                                                               | Není dostupné.                                                                               |
| vyžadovat podepsané tokeny           | Boolean. Určuje, zda je nutné token podepsat.                                                                                                                                                                                                                                                                                                                                                                                           | Ne                                                                               | true                                                                              |
| Oddělovač                       | Řetězec. Určuje oddělovač (např.                                                                                                                                                                                                                                                                                                                                          | Ne                                                                               | Není dostupné.                                                                               |
| url                             | Otevřete adresu URL koncového bodu konfigurace ID, ze kterého lze získat metadata konfigurace Open ID. Odpověď by měla být podle specifikací definovaných na adrese URL:`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`. Pro službu Azure Active `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` Directory použijte následující adresu URL: nahrazení názvu `contoso.onmicrosoft.com`klienta adresáře, například . | Ano                                                                              | Není dostupné.                                                                               |
| output-token-variable-name      | Řetězec. Název kontextové proměnné, která obdrží hodnotu [`Jwt`](api-management-policy-expressions.md) tokenu jako objekt typu při úspěšném ověření tokenu                                                                                                                                                                                                                                                                                     | Ne                                                                               | Není dostupné.                                                                               |

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí
-   **Obory zásad:** všechny obory

## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami naleznete v tématu:

-   [Zásady ve správě rozhraní API](api-management-howto-policies.md)
-   [Transformovat api](transform-api.md)
-   [Odkaz na zásady](api-management-policy-reference.md) pro úplný seznam prohlášení zásad a jejich nastavení
-   [Ukázky zásad](policy-samples.md)
