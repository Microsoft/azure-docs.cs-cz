---
title: Zásady omezení přístupu k Azure API Management | Microsoft Docs
description: Přečtěte si o zásadách omezení přístupu dostupných pro použití v Azure API Management.
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
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902504"
---
# <a name="api-management-access-restriction-policies"></a>Zásady omezení přístupu API Management

V tomto tématu najdete referenční informace pro následující zásady API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady v API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="AccessRestrictionPolicies"></a>Zásady omezení přístupu

-   [Zkontroluje HLAVIČKU http](api-management-access-restriction-policies.md#CheckHTTPHeader) – vynutila existenci nebo hodnotu hlavičky HTTP.
-   [Omezení četnosti volání podle předplatného](api-management-access-restriction-policies.md#LimitCallRate) – zabrání špičkám využití rozhraní API omezením četnosti volání na základě jednotlivých předplatných.
-   [Omezení četnosti volání podle klíče](#LimitCallRateByKey) – zabrání špičkám využití rozhraní API omezením četnosti volání, a to za klíčovým základem.
-   Omezení počtu IP adres volajících – filtry (povolující a [zakazují](api-management-access-restriction-policies.md#RestrictCallerIPs) ) volání z konkrétních IP adres nebo rozsahů adres.
-   [Nastavení kvóty využití podle předplatného](api-management-access-restriction-policies.md#SetUsageQuota) – umožňuje vynutilit obnovitelné nebo maximální objem volání nebo kvótu šířky pásma, a to na základě jednotlivých předplatných.
-   [Nastavení kvóty využití podle klíče](#SetUsageQuotaByKey) – umožňuje vynutilit obnovitelné nebo maximální objem volání nebo kvótu šířky pásma, a to na základě jednotlivých klíčů.
-   Ověří, zda existence [tokenu JWT](api-management-access-restriction-policies.md#ValidateJWT) vynutila platnost a zda byla extrahována z buď zadaného záhlaví protokolu HTTP, nebo zadaného parametru dotazu.

> [!TIP]
> Zásady omezení přístupu můžete použít v různých oborech pro různé účely. Můžete například zabezpečit celé rozhraní API pomocí ověřování AAD, a to tak, že použijete zásady `validate-jwt` na úrovni rozhraní API, nebo ho můžete použít na úrovni operace rozhraní API a použít `claims` k podrobnějšímu řízení.

## <a name="CheckHTTPHeader"></a>Kontrolovat hlavičku HTTP

Pomocí zásad `check-header` vyjistěte, aby požadavek měl určenou hlavičku HTTP. Volitelně můžete kontrolovat, zda má hlavička určitou hodnotu, nebo kontrolovat Rozsah povolených hodnot. Pokud se ověření nepovede, zásada ukončí zpracování žádosti a vrátí stavový kód HTTP a chybovou zprávu určenou zásadou.

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

| Name (Název)         | Popis                                                                                                                                   | Požaduje se |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| check-Header | Kořenový element.                                                                                                                                 | Ano      |
| hodnota        | Povolená hodnota hlavičky protokolu HTTP. Je-li zadána více elementů hodnot, je tato kontrolu považována za úspěšnou, pokud je jedna z hodnot shodná. | Ne       |

### <a name="attributes"></a>Atributy

| Name (Název)                       | Popis                                                                                                                                                            | Požaduje se | Výchozí |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| failed-check-error-message | Chybová zpráva, která se má vrátit v těle odpovědi HTTP, pokud hlavička neexistuje nebo má neplatnou hodnotu. Tato zpráva musí mít správně uvozené speciální znaky. | Ano      | Nevztahuje se     |
| failed-check-httpcode      | Stavový kód HTTP, který se má vrátit, pokud hlavička neexistuje nebo má neplatnou hodnotu.                                                                                        | Ano      | Nevztahuje se     |
| záhlaví – název                | Název hlavičky HTTP, která se má ověřit                                                                                                                                  | Ano      | Nevztahuje se     |
| ignorovat – případ                | Lze nastavit na hodnotu true nebo false. Pokud je hodnota nastavená na true Case, ignoruje se při porovnání hodnoty hlavičky se sadou přijatelných hodnot.                                    | Ano      | Nevztahuje se     |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zásad.

-   **Oddíly zásad:** příchozí, odchozí

-   **Obory zásad:** všechny rozsahy

## <a name="LimitCallRate"></a>Omezení četnosti volání podle předplatného

Zásady `rate-limit` zabrání špičkám využití rozhraní API na základě předplatného, a to omezením rychlosti volání na zadaný počet za zadané časové období. Když se spustí tato zásada, volající obdrží stavový kód odpovědi `429 Too Many Requests`.

> [!IMPORTANT]
> Tato zásada se dá pro dokument zásad použít jenom jednou.
>
> [Výrazy zásad](api-management-policy-expressions.md) nelze použít v žádném z atributů zásad pro tuto zásadu.

> [!CAUTION]
> Vzhledem k distribuované povaze architektury omezování není omezení rychlosti nikdy zcela přesné. Rozdíl mezi nakonfigurovaným a skutečným počtem povolených požadavků se liší v závislosti na objemu a míře požadavků, latenci back-endu a dalších faktorech.

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

| Name (Název)       | Popis                                                                                                                                                                                                                                                                                              | Požaduje se |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| frekvence – omezení | Kořenový element.                                                                                                                                                                                                                                                                                            | Ano      |
| rozhraní api        | Přidejte jeden nebo více těchto prvků k omezení četnosti volání rozhraní API v rámci produktu. Omezení rychlosti volání rozhraní API a produktů se aplikují nezávisle. Na rozhraní API se dá odkazovat buď pomocí `name`, nebo `id`. Pokud jsou zadány oba atributy, `id` budou použity a `name` budou ignorovány.                    | Ne       |
| operation  | Přidejte jeden nebo více těchto prvků k omezení četnosti volání operací v rámci rozhraní API. Omezení frekvence volání produktů, rozhraní API a operací se aplikují nezávisle. Na operaci lze odkazovat pomocí `name` nebo `id`. Pokud jsou zadány oba atributy, `id` budou použity a `name` budou ignorovány. | Ne       |

### <a name="attributes"></a>Atributy

| Name (Název)           | Popis                                                                                           | Požaduje se | Výchozí |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| jméno           | Název rozhraní API, pro které se má použít limit přenosové rychlosti                                                | Ano      | Nevztahuje se     |
| volání          | Maximální celkový počet volání povolených v časovém intervalu zadaném v `renewal-period`. | Ano      | Nevztahuje se     |
| prodloužení platnosti – období | Časové období v sekundách, po kterém se kvóta resetuje.                                              | Ano      | Nevztahuje se     |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zásad.

-   **Oddíly zásad:** příchozí

-   **Obory zásad:** produkt, rozhraní API, operace

## <a name="LimitCallRateByKey"></a>Omezení četnosti volání podle klíče

> [!IMPORTANT]
> Tato funkce není k dispozici v API Management úrovně **spotřeby** .

Zásada `rate-limit-by-key` zabraňuje nárůstu využití rozhraní API na základě klíčů a omezením rychlosti volání na zadaný počet za zadané časové období. Klíč může obsahovat libovolnou řetězcovou hodnotu a obvykle se poskytuje pomocí výrazu zásad. Můžete přidat volitelnou podmínku přírůstku, která určuje, které požadavky se mají do limitu počítat. Když se spustí tato zásada, volající obdrží stavový kód odpovědi `429 Too Many Requests`.

Další informace a příklady těchto zásad najdete v tématu [Pokročilé omezování požadavků pomocí Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

> [!CAUTION]
> Vzhledem k distribuované povaze architektury omezování není omezení rychlosti nikdy zcela přesné. Rozdíl mezi nakonfigurovaným a skutečným počtem povolených požadavků se liší v závislosti na objemu a míře požadavků, latenci back-endu a dalších faktorech.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" />

```

### <a name="example"></a>Příklad:

V následujícím příkladu je omezení přenosové rychlosti nastaveno podle IP adresy volajícího.

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
| rate-limit-by-key | Kořenový element. | Ano      |

### <a name="attributes"></a>Atributy

| Name (Název)                | Popis                                                                                           | Požaduje se | Výchozí |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| volání               | Maximální celkový počet volání povolených v časovém intervalu zadaném v `renewal-period`. | Ano      | Nevztahuje se     |
| Counter – klíč         | Klíč, který se má použít pro zásady omezení četnosti.                                                             | Ano      | Nevztahuje se     |
| přírůstek-podmínka | Logický výraz určující, zda má být požadavek počítán k kvótě (`true`).        | Ne       | Nevztahuje se     |
| prodloužení platnosti – období      | Časové období v sekundách, po kterém se kvóta resetuje.                                              | Ano      | Nevztahuje se     |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zásad.

-   **Oddíly zásad:** příchozí

-   **Obory zásad:** všechny rozsahy

## <a name="RestrictCallerIPs"></a>Omezení IP adres volajícího

Zásady `ip-filter` filtr (povolují/zakazuje) volání z konkrétních IP adres nebo rozsahů adres.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Příklad:

V následujícím příkladu zásada povoluje pouze žádosti přicházející buď z jedné IP adresy, nebo z rozsahu zadaných IP adres.

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Elementy

| Name (Název)                                      | Popis                                         | Požaduje se                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-filter                                 | Kořenový element.                                       | Ano                                                            |
| adresa                                   | Určuje jednu IP adresu, na které se má filtrovat.   | Vyžaduje se aspoň jeden `address` nebo element `address-range`. |
| adresový rozsah z = "adresa" na = "adresa" | Určuje rozsah IP adres, na kterých se má filtrovat. | Vyžaduje se aspoň jeden `address` nebo element `address-range`. |

### <a name="attributes"></a>Atributy

| Name (Název)                                      | Popis                                                                                 | Požaduje se                                           | Výchozí |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| adresový rozsah z = "adresa" na = "adresa" | Rozsah IP adres, pro které chcete povolit nebo odepřít přístup.                                        | Požadováno při použití prvku `address-range`. | Nevztahuje se     |
| akce filtru IP = "povolení &#124; zakazuje"    | Určuje, jestli se mají u zadaných IP adres a rozsahů povolit volání. | Ano                                                | Nevztahuje se     |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zásad.

-   **Oddíly zásad:** příchozí
-   **Obory zásad:** všechny rozsahy

## <a name="SetUsageQuota"></a>Nastavení kvóty využití podle předplatného

Zásada `quota` vynutila obnovitelné nebo maximální objem volání nebo kvótu šířky pásma na základě jednotlivých předplatných.

> [!IMPORTANT]
> Tato zásada se dá pro dokument zásad použít jenom jednou.
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

| Name (Název)      | Popis                                                                                                                                                                                                                                                                                  | Požaduje se |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| kvóta     | Kořenový element.                                                                                                                                                                                                                                                                                | Ano      |
| rozhraní api       | Přidejte jeden nebo více těchto prvků pro uložení kvóty volání rozhraní API v rámci produktu. Kvóty volání produktů a rozhraní API se aplikují nezávisle. Na rozhraní API se dá odkazovat buď pomocí `name`, nebo `id`. Pokud jsou zadány oba atributy, `id` budou použity a `name` budou ignorovány.                    | Ne       |
| operation | Přidejte jeden nebo více těchto prvků pro uložení kvóty volání operací v rámci rozhraní API. Kvóty volání produktů, rozhraní API a operací se aplikují nezávisle. Na operaci lze odkazovat pomocí `name` nebo `id`. Pokud jsou zadány oba atributy, `id` budou použity a `name` budou ignorovány. | Ne       |

### <a name="attributes"></a>Atributy

| Name (Název)           | Popis                                                                                               | Požaduje se                                                         | Výchozí |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| jméno           | Název rozhraní API nebo operace, pro kterou platí kvóta.                                             | Ano                                                              | Nevztahuje se     |
| šířka pásma      | Maximální celkový počet kilobajtů povolený v časovém intervalu zadaném v `renewal-period`. | Musí být zadán buď `calls`, `bandwidth`, nebo oba současně. | Nevztahuje se     |
| volání          | Maximální celkový počet volání povolených v časovém intervalu zadaném v `renewal-period`.     | Musí být zadán buď `calls`, `bandwidth`, nebo oba současně. | Nevztahuje se     |
| prodloužení platnosti – období | Časové období v sekundách, po kterém se kvóta resetuje.                                                  | Ano                                                              | Nevztahuje se     |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zásad.

-   **Oddíly zásad:** příchozí
-   **Obory zásad:** produkt

## <a name="SetUsageQuotaByKey"></a>Nastavit kvótu využití podle klíče

> [!IMPORTANT]
> Tato funkce není k dispozici v API Management úrovně **spotřeby** .

Zásada `quota-by-key` vynutila obnovitelné nebo maximální objem volání nebo kvótu šířky pásma na jednotlivých klíčích. Klíč může obsahovat libovolnou řetězcovou hodnotu a obvykle se poskytuje pomocí výrazu zásad. Můžete přidat volitelnou podmínku přírůstku, která určuje, které požadavky se mají do kvóty počítat. Pokud by více zásad mohl zvýšit stejnou hodnotu klíče, zvyšuje se pouze jednou za požadavek. Po dosažení limitu volání dostane volající kód stavu odpovědi `403 Forbidden`.

Další informace a příklady těchto zásad najdete v tématu [Pokročilé omezování požadavků pomocí Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Příklad:

V následujícím příkladu je kvóta nastavena podle IP adresy volajícího.

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
| kvóta | Kořenový element. | Ano      |

### <a name="attributes"></a>Atributy

| Name (Název)                | Popis                                                                                               | Požaduje se                                                         | Výchozí |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| šířka pásma           | Maximální celkový počet kilobajtů povolený v časovém intervalu zadaném v `renewal-period`. | Musí být zadán buď `calls`, `bandwidth`, nebo oba současně. | Nevztahuje se     |
| volání               | Maximální celkový počet volání povolených v časovém intervalu zadaném v `renewal-period`.     | Musí být zadán buď `calls`, `bandwidth`, nebo oba současně. | Nevztahuje se     |
| Counter – klíč         | Klíč, který se má použít pro zásady kvót                                                                      | Ano                                                              | Nevztahuje se     |
| přírůstek-podmínka | Logický výraz určující, zda má být požadavek počítán k kvótě (`true`)             | Ne                                                               | Nevztahuje se     |
| prodloužení platnosti – období      | Časové období v sekundách, po kterém se kvóta resetuje.                                                  | Ano                                                              | Nevztahuje se     |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zásad.

-   **Oddíly zásad:** příchozí
-   **Obory zásad:** všechny rozsahy

## <a name="ValidateJWT"></a>Ověřit token JWT

Zásady `validate-jwt` vynutily existenci a platnost získaného JWT buď ze zadané hlavičky protokolu HTTP, nebo ze zadaného parametru dotazu.

> [!IMPORTANT]
> Zásady `validate-jwt` vyžadují, aby se deklarace identity zaregistrovaná `exp` zahrnovala v tokenu JWT, pokud není zadaný atribut `require-expiration-time` a nastaven na `false`.
> Zásady `validate-jwt` podporují algoritmy podepisování HS256 a RS256. Pro HS256 musí být klíč poskytnutý jako vložený v rámci zásady ve formě kódované v kódování Base64. Pro RS256 je nutné klíč poskytnout prostřednictvím koncového bodu konfigurace otevřeného ID.
> Zásady `validate-jwt` podporují tokeny šifrované pomocí symetrických klíčů pomocí následujících šifrovacích algoritmů A128CBC-HS256, A192CBC-HS384, A256CBC-HS512.

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

#### <a name="simple-token-validation"></a>Ověřování jednoduchých tokenů

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

#### <a name="azure-active-directory-token-validation"></a>Ověřování tokenu Azure Active Directory

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

#### <a name="azure-active-directory-b2c-token-validation"></a>Ověřování tokenu Azure Active Directory B2C

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

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Autorizace přístupu k operacím na základě deklarací identity tokenů

Tento příklad ukazuje, jak použít zásadu [ověření JWT](api-management-access-restriction-policies.md#ValidateJWT) k autorizaci přístupu k operacím na základě hodnoty deklarací identity tokenu.

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
| validate-jwt        | Kořenový element.                                                                                                                                                                                                                                                                                                                                         | Ano      |
| publikum           | Obsahuje seznam přijatelných deklarací cílové skupiny, které mohou být k dispozici na tokenu. Pokud je přítomno více hodnot cílové skupiny, pak se každá hodnota vyzkouší, dokud nebudou vyčerpány všechny (v takovém případě ověření selže) nebo dokud jeden neuspěje. Je nutné zadat alespoň jednu cílovou skupinu.                                                                     | Ne       |
| issuer-signing-keys | Seznam klíčů zabezpečení kódovaných v kódování Base64 používaných k ověřování podepsaných tokenů. Pokud je k dispozici více klíčů zabezpečení, pak se každý klíč vyzkouší, dokud nebudou vyčerpány všechny (v takovém případě ověření selže) nebo dokud jedna neuspěje (užitečné pro výměnu tokenu). Klíčové prvky mají volitelný atribut `id`, který se používá pro spárování s deklarací `kid`.               | Ne       |
| dešifrování – klíče     | Seznam klíčů zakódovaných ve formátu base64, které slouží k dešifrování tokenů. Pokud je k dispozici více klíčů zabezpečení, pak se každý klíč vyzkouší, dokud nebudou vyčerpány všechny klíče (v takovém případě ověření selže) nebo dokud nebude klíč úspěšný. Klíčové prvky mají volitelný atribut `id`, který se používá pro spárování s deklarací `kid`.                                                 | Ne       |
| vystavitelů             | Seznam přijatelných objektů zabezpečení, které token vystavily. Pokud je přítomno více hodnot vystavitelů, pak se každá hodnota vyzkouší, dokud nejsou vyčerpány všechny (v takovém případě ověření selže) nebo dokud jeden neuspěje.                                                                                                                                         | Ne       |
| OpenID-config       | Prvek použitý k zadání koncového bodu konfigurace kompatibilního otevřeného ID, ze kterého lze získat podpisové klíče a vystavitele.                                                                                                                                                                                                                        | Ne       |
| požadováno – deklarace identity     | Obsahuje seznam deklarací identity, které mají být přítomny na tokenu, aby se dalo považovat za platný. Pokud je atribut `match` nastavený na `all` musí být v tokenu přítomná každá hodnota deklarace identity, aby bylo ověření úspěšné. Pokud je atribut `match` nastavený na `any` aspoň jedna deklarace identity musí být přítomná v tokenu, aby bylo ověření úspěšné. | Ne       |

### <a name="attributes"></a>Atributy

| Name (Název)                            | Popis                                                                                                                                                                                                                                                                                                                                                                                                                                            | Požaduje se                                                                         | Výchozí                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| hodiny – zkosit                      | TimeSpan. Slouží k zadání maximálního očekávaného časového rozdílu mezi systémovými hodinami vystavitele tokenu a instancí API Management.                                                                                                                                                                                                                                                                                                               | Ne                                                                               | 0 sekund                                                                         |
| Chyba-ověření-zpráva | Chybová zpráva, která se má vrátit v těle odpovědi HTTP, pokud metoda JWT neprojde ověřením. Tato zpráva musí mít správně uvozené speciální znaky.                                                                                                                                                                                                                                                                                                 | Ne                                                                               | Výchozí chybová zpráva závisí na potížích s ověřením, například "JWT není k dispozici". |
| Chyba-ověření – httpCode      | Stavový kód HTTP, který se má vrátit, pokud metoda JWT neprojde ověřením.                                                                                                                                                                                                                                                                                                                                                                                         | Ne                                                                               | 401                                                                               |
| záhlaví – název                     | Název hlavičky protokolu HTTP, která drží token.                                                                                                                                                                                                                                                                                                                                                                                                         | Je nutné zadat jeden z `header-name``query-parameter-name` nebo `token-value`. | Nevztahuje se                                                                               |
| dotaz-parametr-název            | Název parametru dotazu, který drží token.                                                                                                                                                                                                                                                                                                                                                                                                     | Je nutné zadat jeden z `header-name``query-parameter-name` nebo `token-value`. | Nevztahuje se                                                                               |
| hodnota tokenu                     | Výraz vracející řetězec obsahující token JWT                                                                                                                                                                                                                                                                                                                                                                                                     | Je nutné zadat jeden z `header-name``query-parameter-name` nebo `token-value`. | Nevztahuje se                                                                               |
| id                              | Atribut `id` elementu `key` umožňuje zadat řetězec, který se bude shodovat s `kid` deklarací identity v tokenu (Pokud je k dispozici) a zjistit odpovídající klíč pro ověření podpisu.                                                                                                                                                                                                                                           | Ne                                                                               | Nevztahuje se                                                                               |
| match                           | Atribut `match` u elementu `claim` určuje, zda musí být v tokenu přítomna každá hodnota deklarace identity, aby bylo ověření úspěšné. Možné hodnoty:<br /><br /> - `all` – každá hodnota deklarace identity v zásadě musí být přítomná v tokenu, aby bylo ověření úspěšné.<br /><br /> - `any`-v tokenu musí být alespoň jedna hodnota deklarace identity, aby bylo ověření úspěšné.                                                       | Ne                                                                               | Vše                                                                               |
| vyžadovat – čas vypršení platnosti         | Datového. Určuje, jestli je v tokenu vyžadována deklarace identity vypršení platnosti.                                                                                                                                                                                                                                                                                                                                                                               | Ne                                                                               | true                                                                              |
| require-scheme                  | Název schématu tokenu, např. "nosič". Pokud je tento atribut nastavený, zásada ověří, jestli je v hodnotě autorizační hlavičky zadané schéma.                                                                                                                                                                                                                                                                                    | Ne                                                                               | Nevztahuje se                                                                               |
| vyžadovat – podepsané tokeny           | Datového. Určuje, zda je vyžadován token, který má být podepsán.                                                                                                                                                                                                                                                                                                                                                                                           | Ne                                                                               | true                                                                              |
| oddělování                       | Řetězec. Určuje oddělovač (například ","), který se použije k extrakci sady hodnot z deklarace s více hodnotami.                                                                                                                                                                                                                                                                                                                                          | Ne                                                                               | Nevztahuje se                                                                               |
| url                             | Otevřete adresu URL koncového bodu konfigurace ID, ze kterého lze získat metadata konfigurace Open ID. Odpověď by měla být podle specifikací definovaných na adrese URL:`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`. Pro Azure Active Directory použijte následující adresu URL: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` nahrazení názvu tenanta adresáře, třeba `contoso.onmicrosoft.com`. | Ano                                                                              | Nevztahuje se                                                                               |
| výstup-token-proměnná-Name      | Řetězec. Název kontextové proměnné, která obdrží hodnotu tokenu jako objekt typu [`Jwt`](api-management-policy-expressions.md) po úspěšném ověření tokenu                                                                                                                                                                                                                                                                                     | Ne                                                                               | Nevztahuje se                                                                               |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zásad.

-   **Oddíly zásad:** příchozí
-   **Obory zásad:** všechny rozsahy

## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami najdete v těchto tématech:

-   [Zásady v API Management](api-management-howto-policies.md)
-   [Transformační rozhraní API](transform-api.md)
-   [Odkaz na zásady](api-management-policy-reference.md) pro úplný seznam příkazů zásad a jejich nastavení
-   [Ukázky zásad](policy-samples.md)
