---
title: Zásady omezení přístupu k Azure API Management | Microsoft Docs
description: Přečtěte si o zásadách omezení přístupu dostupných pro použití v Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.topic: article
ms.date: 02/26/2021
ms.author: apimpm
ms.openlocfilehash: 882d96271b6976db1ffc0dde181d5699c5cc27de
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688242"
---
# <a name="api-management-access-restriction-policies"></a>Zásady omezení přístupu ke službě API Management

V tomto tématu najdete referenční informace pro následující zásady API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady v API Management](./api-management-policies.md).

## <a name="access-restriction-policies"></a><a name="AccessRestrictionPolicies"></a> Zásady omezení přístupu

-   [Zkontroluje HLAVIČKU http](#CheckHTTPHeader) – vynutila existenci nebo hodnotu hlavičky HTTP.
-   [Omezení četnosti volání podle předplatného](#LimitCallRate) – zabrání špičkám využití rozhraní API omezením četnosti volání na základě jednotlivých předplatných.
-   [Omezení četnosti volání podle klíče](#LimitCallRateByKey) – zabrání špičkám využití rozhraní API omezením četnosti volání, a to za klíčovým základem.
-   Omezení počtu IP adres volajících – filtry (povolující a [zakazují](#RestrictCallerIPs) ) volání z konkrétních IP adres nebo rozsahů adres.
-   [Nastavení kvóty využití podle předplatného](#SetUsageQuota) – umožňuje vynutilit obnovitelné nebo maximální objem volání nebo kvótu šířky pásma, a to na základě jednotlivých předplatných.
-   [Nastavení kvóty využití podle klíče](#SetUsageQuotaByKey) – umožňuje vynutilit obnovitelné nebo maximální objem volání nebo kvótu šířky pásma, a to na základě jednotlivých klíčů.
-   Ověří, zda existence [tokenu JWT](#ValidateJWT) vynutila platnost a zda byla extrahována z buď zadaného záhlaví protokolu HTTP, nebo zadaného parametru dotazu.

> [!TIP]
> Zásady omezení přístupu můžete použít v různých oborech pro různé účely. Můžete například zabezpečit celé rozhraní API pomocí ověřování AAD, a to tak, že použijete `validate-jwt` zásady na úrovni rozhraní API, nebo ho můžete použít na úrovni operace rozhraní API a použít k podrobnějšímu `claims` řízení.

## <a name="check-http-header"></a><a name="CheckHTTPHeader"></a> Kontrolovat hlavičku HTTP

Pomocí `check-header` zásady můžete vyhovět, že žádost má určenou HLAVIČKU http. Volitelně můžete kontrolovat, zda má hlavička určitou hodnotu, nebo kontrolovat Rozsah povolených hodnot. Pokud se ověření nepovede, zásada ukončí zpracování žádosti a vrátí stavový kód HTTP a chybovou zprávu určenou zásadou.

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

| Název         | Popis                                                                                                                                   | Povinné |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| check-Header | Kořenový element.                                                                                                                                 | Ano      |
| hodnota        | Povolená hodnota hlavičky protokolu HTTP. Je-li zadána více elementů hodnot, je tato kontrolu považována za úspěšnou, pokud je jedna z hodnot shodná. | Ne       |

### <a name="attributes"></a>Atributy

| Název                       | Popis                                                                                                                                                            | Povinné | Výchozí |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| neúspěšné-chyba-chyba-zpráva | Chybová zpráva, která se má vrátit v těle odpovědi HTTP, pokud hlavička neexistuje nebo má neplatnou hodnotu. Tato zpráva musí mít správně uvozené speciální znaky. | Ano      | –     |
| chyba-check-httpCode      | Stavový kód HTTP, který se má vrátit, pokud hlavička neexistuje nebo má neplatnou hodnotu.                                                                                        | Ano      | –     |
| záhlaví – název                | Název hlavičky HTTP, která se má ověřit                                                                                                                                  | Ano      | –     |
| ignorovat – případ                | Lze nastavit na hodnotu true nebo false. Pokud je hodnota nastavená na true Case, ignoruje se při porovnání hodnoty hlavičky se sadou přijatelných hodnot.                                    | Ano      | –     |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, odchozí

-   **Obory zásad:** všechny rozsahy

## <a name="limit-call-rate-by-subscription"></a><a name="LimitCallRate"></a> Omezení četnosti volání podle předplatného

`rate-limit`Zásady zabrání špičkám využití rozhraní API na základě předplatného, a to omezením rychlosti volání na zadaný počet za zadané časové období. Pokud je překročena rychlost volání, volající obdrží `429 Too Many Requests` kód stavu odpovědi.

> [!IMPORTANT]
> Tato zásada se dá pro dokument zásad použít jenom jednou.
>
> [Výrazy zásad](api-management-policy-expressions.md) nelze použít v žádném z atributů zásad pro tuto zásadu.

> [!CAUTION]
> Vzhledem k distribuované povaze architektury omezování není omezení rychlosti nikdy zcela přesné. Rozdíl mezi nakonfigurovaným a skutečným počtem povolených požadavků se liší v závislosti na objemu a míře požadavků, latenci back-endu a dalších faktorech.

> [!NOTE]
> Pro pochopení rozdílu mezi omezeními a kvótami získáte [informace v části omezení přenosové rychlosti a kvóty.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" 
        retry-after-header-name="header name" 
        retry-after-variable-name="policy expression variable name"
        remaining-calls-header-name="header name"  
        remaining-calls-variable-name="policy expression variable name"
        total-calls-header-name="header name"/>
    </api>
</rate-limit>
```

### <a name="example"></a>Příklad

V následujícím příkladu je omezení četnosti předplatného na 20 volání za 90 sekund. Po každém spuštění zásad jsou zbývající volání povolená v daném časovém období uložená v proměnné `remainingCallsPerSubscription` .

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" remaining-calls-variable-name="remainingCallsPerSubscription"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementy

| Název       | Popis                                                                                                                                                                                                                                                                                              | Povinné |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| frekvence – omezení | Kořenový element.                                                                                                                                                                                                                                                                                            | Ano      |
| api        | Přidejte jeden nebo více těchto prvků k omezení četnosti volání rozhraní API v rámci produktu. Omezení rychlosti volání rozhraní API a produktů se aplikují nezávisle. Na rozhraní API se dá odkazovat prostřednictvím `name` nebo `id` . Pokud jsou zadány oba atributy, budou `id` použity a `name` budou ignorovány.                    | Ne       |
| operation  | Přidejte jeden nebo více těchto prvků k omezení četnosti volání operací v rámci rozhraní API. Omezení frekvence volání produktů, rozhraní API a operací se aplikují nezávisle. Na operaci lze odkazovat prostřednictvím `name` nebo `id` . Pokud jsou zadány oba atributy, budou `id` použity a `name` budou ignorovány. | Ne       |

### <a name="attributes"></a>Atributy

| Název           | Popis                                                                                           | Povinné | Výchozí |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| name           | Název rozhraní API, pro které se má použít limit přenosové rychlosti                                                | Ano      | –     |
| volání          | Maximální celkový počet volání povolených v časovém intervalu zadaném v `renewal-period` . | Ano      | –     |
| prodloužení platnosti – období | Délka v sekundách posuvných oken, během kterých počet povolených požadavků nesmí ani překročil hodnotu určenou v poli `calls` .                                              | Ano      | –     |
| opakovat za-záhlaví-název    | Název hlavičky odpovědi, jejíž hodnota je doporučeným intervalem opakování v sekundách po překročení zadané rychlosti volání. |  Ne | –  |
| retry-za-Variable-Name    | Název proměnné výrazu zásad, která ukládá Doporučený interval opakování v sekundách po překročení zadané rychlosti volání. |  Ne | –  |
| zbývající – volání-záhlaví – název    | Název hlavičky odpovědi, jejíž hodnota po každém spuštění zásad je počet zbývajících volání povolených v časovém intervalu zadaném v `renewal-period` . |  Ne | –  |
| zbývající – volání – proměnná-Name    | Název proměnné výrazu zásady, který po každém spuštění zásad ukládá počet zbývajících volání povolených v časovém intervalu zadaném v `renewal-period` . |  Ne | –  |
| Celkem – volání-záhlaví – název    | Název hlavičky odpovědi, jejíž hodnota je hodnota zadaná v poli `calls` . |  Ne | –  |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí

-   **Obory zásad:** produkt, rozhraní API, operace

## <a name="limit-call-rate-by-key"></a><a name="LimitCallRateByKey"></a> Omezení četnosti volání podle klíče

> [!IMPORTANT]
> Tato funkce není k dispozici v API Management úrovně **spotřeby** .

`rate-limit-by-key`Zásady zabrání využití rozhraní API na základě klíčů a omezením rychlosti volání na zadaný počet za zadané časové období. Klíč může obsahovat libovolnou řetězcovou hodnotu a obvykle se poskytuje pomocí výrazu zásad. Můžete přidat volitelnou podmínku přírůstku, která určuje, které požadavky se mají do limitu počítat. Pokud je překročena Tato rychlost volání, volající obdrží `429 Too Many Requests` kód stavu odpovědi.

Další informace a příklady těchto zásad najdete v tématu [Pokročilé omezování požadavků pomocí Azure API Management](./api-management-sample-flexible-throttling.md).

> [!CAUTION]
> Vzhledem k distribuované povaze architektury omezování není omezení rychlosti nikdy zcela přesné. Rozdíl mezi nakonfigurovaným a skutečným počtem povolených požadavků se liší v závislosti na objemu a míře požadavků, latenci back-endu a dalších faktorech.

> [!NOTE]
> Pro pochopení rozdílu mezi omezeními a kvótami získáte [informace v části omezení přenosové rychlosti a kvóty.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" 
                   retry-after-header-name="header name" retry-after-variable-name="policy expression variable name"
                   remaining-calls-header-name="header name"  remaining-calls-variable-name="policy expression variable name"
                   total-calls-header-name="header name"/> 

```

### <a name="example"></a>Příklad

V následujícím příkladu je omezení přenosové rychlosti 10 volání za 60 sekund označeno IP adresou volajícího. Po každém spuštění zásad jsou zbývající volání povolená v daném časovém období uložená v proměnné `remainingCallsPerIP` .

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"
              remaining-calls-variable-name="remainingCallsPerIP"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementy

| Název              | Popis   | Povinné |
| ----------------- | ------------- | -------- |
| rychlost – omezení podle klíče | Kořenový element. | Ano      |

### <a name="attributes"></a>Atributy

| Název                | Popis                                                                                           | Povinné | Výchozí |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| volání               | Maximální celkový počet volání povolených v časovém intervalu zadaném v `renewal-period` . | Ano      | –     |
| Counter – klíč         | Klíč, který se má použít pro zásady omezení četnosti.                                                             | Ano      | –     |
| přírůstek-podmínka | Logický výraz určující, zda má být požadavek počítán směrem k sazbě ( `true` ).        | Ne       | –     |
| prodloužení platnosti – období      | Délka v sekundách posuvných oken, během kterých počet povolených požadavků nesmí ani překročil hodnotu určenou v poli `calls` .                                           | Ano      | –     |
| opakovat za-záhlaví-název    | Název hlavičky odpovědi, jejíž hodnota je doporučeným intervalem opakování v sekundách po překročení zadané rychlosti volání. |  Ne | –  |
| retry-za-Variable-Name    | Název proměnné výrazu zásad, která ukládá Doporučený interval opakování v sekundách po překročení zadané rychlosti volání. |  Ne | –  |
| zbývající – volání-záhlaví – název    | Název hlavičky odpovědi, jejíž hodnota po každém spuštění zásad je počet zbývajících volání povolených v časovém intervalu zadaném v `renewal-period` . |  Ne | –  |
| zbývající – volání – proměnná-Name    | Název proměnné výrazu zásady, který po každém spuštění zásad ukládá počet zbývajících volání povolených v časovém intervalu zadaném v `renewal-period` . |  Ne | –  |
| Celkem – volání-záhlaví – název    | Název hlavičky odpovědi, jejíž hodnota je hodnota zadaná v poli `calls` . |  Ne | –  |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí

-   **Obory zásad:** všechny rozsahy

## <a name="restrict-caller-ips"></a><a name="RestrictCallerIPs"></a> Omezení IP adres volajícího

`ip-filter`Filtry zásad (povolují/zakazuje) volání z konkrétních IP adres nebo rozsahů adres.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Příklad

V následujícím příkladu zásada povoluje pouze žádosti přicházející buď z jedné IP adresy, nebo z rozsahu zadaných IP adres.

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Elementy

| Název                                      | Popis                                         | Povinné                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| filtr IP adres                                 | Kořenový element.                                       | Ano                                                            |
| adresa                                   | Určuje jednu IP adresu, na které se má filtrovat.   | `address`Je vyžadován alespoň jeden `address-range` prvek nebo. |
| adresový rozsah z = "adresa" na = "adresa" | Určuje rozsah IP adres, na kterých se má filtrovat. | `address`Je vyžadován alespoň jeden `address-range` prvek nebo. |

### <a name="attributes"></a>Atributy

| Název                                      | Popis                                                                                 | Povinné                                           | Výchozí |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| adresový rozsah z = "adresa" na = "adresa" | Rozsah IP adres, pro které chcete povolit nebo odepřít přístup.                                        | Požadováno při `address-range` použití elementu. | –     |
| akce filtru IP = "povolení &#124; zakázat"    | Určuje, jestli se mají u zadaných IP adres a rozsahů povolit volání. | Ano                                                | –     |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí
-   **Obory zásad:** všechny rozsahy

## <a name="set-usage-quota-by-subscription"></a><a name="SetUsageQuota"></a> Nastavení kvóty využití podle předplatného

Tato `quota` zásada vynutila obnovitelné nebo maximální objem volání nebo kvótu šířky pásma na jednotlivých předplatných.

> [!IMPORTANT]
> Tato zásada se dá pro dokument zásad použít jenom jednou.
>
> [Výrazy zásad](api-management-policy-expressions.md) nelze použít v žádném z atributů zásad pro tuto zásadu.

> [!NOTE]
> Pro pochopení rozdílu mezi omezeními a kvótami získáte [informace v části omezení přenosové rychlosti a kvóty.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

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

| Název      | Popis                                                                                                                                                                                                                                                                                  | Povinné |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| kvóta     | Kořenový element.                                                                                                                                                                                                                                                                                | Ano      |
| api       | Přidejte jeden nebo více těchto prvků pro uložení kvóty volání rozhraní API v rámci produktu. Kvóty volání produktů a rozhraní API se aplikují nezávisle. Na rozhraní API se dá odkazovat prostřednictvím `name` nebo `id` . Pokud jsou zadány oba atributy, budou `id` použity a `name` budou ignorovány.                    | Ne       |
| operation | Přidejte jeden nebo více těchto prvků pro uložení kvóty volání operací v rámci rozhraní API. Kvóty volání produktů, rozhraní API a operací se aplikují nezávisle. Na operaci lze odkazovat prostřednictvím `name` nebo `id` . Pokud jsou zadány oba atributy, budou `id` použity a `name` budou ignorovány. | Ne      |

### <a name="attributes"></a>Atributy

| Název           | Popis                                                                                               | Povinné                                                         | Výchozí |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| name           | Název rozhraní API nebo operace, pro kterou platí kvóta.                                             | Ano                                                              | –     |
| připojení      | Maximální celkový počet kilobajtů povolený v časovém intervalu zadaném v `renewal-period` . | `calls` `bandwidth` Musí být zadány oba, nebo oba současně. | –     |
| volání          | Maximální celkový počet volání povolených v časovém intervalu zadaném v `renewal-period` .     | `calls` `bandwidth` Musí být zadány oba, nebo oba současně. | –     |
| prodloužení platnosti – období | Časové období v sekundách, po kterém se kvóta resetuje. Pokud je nastavená na `0` perioda, nastaví se na nekonečné. | Ano                                                              | –     |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí
-   **Obory zásad:** produkt

## <a name="set-usage-quota-by-key"></a><a name="SetUsageQuotaByKey"></a> Nastavit kvótu využití podle klíče

> [!IMPORTANT]
> Tato funkce není k dispozici v API Management úrovně **spotřeby** .

Tato `quota-by-key` zásada vynutila obnovitelné nebo maximální objem volání nebo kvótu šířky pásma na jednotlivých klíčích. Klíč může obsahovat libovolnou řetězcovou hodnotu a obvykle se poskytuje pomocí výrazu zásad. Můžete přidat volitelnou podmínku přírůstku, která určuje, které požadavky se mají do kvóty počítat. Pokud by více zásad mohl zvýšit stejnou hodnotu klíče, zvyšuje se pouze jednou za požadavek. Pokud je překročena rychlost volání, volající obdrží `403 Forbidden` kód stavu odpovědi.

Další informace a příklady těchto zásad najdete v tématu [Pokročilé omezování požadavků pomocí Azure API Management](./api-management-sample-flexible-throttling.md).

> [!NOTE]
> Pro pochopení rozdílu mezi omezeními a kvótami získáte [informace v části omezení přenosové rychlosti a kvóty.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Příklad

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

| Název  | Popis   | Povinné |
| ----- | ------------- | -------- |
| kvóta | Kořenový element. | Ano      |

### <a name="attributes"></a>Atributy

| Název                | Popis                                                                                               | Povinné                                                         | Výchozí |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| připojení           | Maximální celkový počet kilobajtů povolený v časovém intervalu zadaném v `renewal-period` . | `calls` `bandwidth` Musí být zadány oba, nebo oba současně. | –     |
| volání               | Maximální celkový počet volání povolených v časovém intervalu zadaném v `renewal-period` .     | `calls` `bandwidth` Musí být zadány oba, nebo oba současně. | –     |
| Counter – klíč         | Klíč, který se má použít pro zásady kvót                                                                      | Ano                                                              | –     |
| přírůstek-podmínka | Logický výraz určující, zda má být požadavek počítán k kvótě ( `true` )             | Ne                                                               | –     |
| prodloužení platnosti – období      | Časové období v sekundách, po kterém se kvóta resetuje. Pokud je nastavená na `0` perioda, nastaví se na nekonečné.                                                   | Ano                                                              | –     |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí
-   **Obory zásad:** všechny rozsahy

## <a name="validate-jwt"></a><a name="ValidateJWT"></a> Ověřit token JWT

`validate-jwt`Zásady vynutily existenci a platnost tokenu JWT (JSON web token) extrahované ze zadané HLAVIČKY http nebo zadaného parametru dotazu.

> [!IMPORTANT]
> Tato `validate-jwt` zásada vyžaduje, aby `exp` registrovaná deklarace identity byla obsažena v tokenu JWT, pokud `require-expiration-time` není zadána vlastnost a nastavena na `false` .
> Tato `validate-jwt` zásada podporuje algoritmy podepisování HS256 a RS256. Pro HS256 musí být klíč poskytnutý jako vložený v rámci zásady ve formě kódované v kódování Base64. Pro RS256 může být klíč poskytnutý buď prostřednictvím koncového bodu konfigurace otevřeného ID, nebo zadáním ID nahraného certifikátu, který obsahuje veřejný klíč nebo dvojici exponentu veřejného klíče.
> `validate-jwt`Zásady podporují tokeny šifrované pomocí symetrických klíčů pomocí následujících šifrovacích algoritmů: A128CBC-HS256, A192CBC-HS384, A256CBC-HS512.

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

#### <a name="token-validation-with-rsa-certificate"></a>Ověřování tokenu pomocí certifikátu RSA

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key certificate-id="my-rsa-cert" />  <!-- signing key specified as certificate ID, enclosed in double-quotes -->
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

| Element             | Popis                                                                                                                                                                                                                                                                                                                                           | Povinné |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| ověřit – JWT        | Kořenový element.                                                                                                                                                                                                                                                                                                                                         | Ano      |
| publikum           | Obsahuje seznam přijatelných deklarací cílové skupiny, které mohou být k dispozici na tokenu. Pokud je přítomno více hodnot cílové skupiny, pak se každá hodnota vyzkouší, dokud nebudou vyčerpány všechny (v takovém případě ověření selže) nebo dokud jeden neuspěje. Je nutné zadat alespoň jednu cílovou skupinu.                                                                     | Ne       |
| Vystavitel – podpisové klíče | Seznam klíčů zabezpečení kódovaných v kódování Base64 používaných k ověřování podepsaných tokenů. Pokud je k dispozici více klíčů zabezpečení, pak se každý klíč vyzkouší, dokud nebudou vyčerpány všechny (v takovém případě selže ověřování) nebo jedna úspěšná (užitečná pro výměnu tokenu). Klíčové prvky mají volitelný `id` atribut, který se používá pro porovnání s `kid` deklarací identity. <br/><br/>Případně zadejte podpisový klíč vystavitele pomocí:<br/><br/> - `certificate-id` v části formát `<key certificate-id="mycertificate" />` Zadejte identifikátor entity certifikátu [nahrané](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity#Add) do API Management<br/>– `n` Páry a exponenty RSA `e` ve formátu `<key n="<modulus>" e="<exponent>" />` pro určení parametrů RSA ve formátu kódovaném v base64url               | Ne       |
| dešifrování – klíče     | Seznam klíčů zakódovaných ve formátu base64, které slouží k dešifrování tokenů. Pokud je k dispozici více klíčů zabezpečení, pak se každý klíč vyzkouší, dokud nebudou vyčerpány všechny klíče (v takovém případě ověření selže) nebo je klíč úspěšný. Klíčové prvky mají volitelný `id` atribut, který se používá pro porovnání s `kid` deklarací identity.<br/><br/>Případně zadejte dešifrovací klíč pomocí:<br/><br/> - `certificate-id` v části formát `<key certificate-id="mycertificate" />` Zadejte identifikátor entity certifikátu [nahrané](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity#Add) do API Management                                                 | Ne       |
| vystavitelů             | Seznam přijatelných objektů zabezpečení, které token vystavily. Pokud je přítomno více hodnot vystavitelů, pak se každá hodnota vyzkouší, dokud nejsou vyčerpány všechny (v takovém případě ověření selže) nebo dokud jeden neuspěje.                                                                                                                                         | Ne       |
| OpenID-config       | Prvek použitý k zadání koncového bodu konfigurace kompatibilního otevřeného ID, ze kterého lze získat podpisové klíče a vystavitele.                                                                                                                                                                                                                        | Ne       |
| požadováno – deklarace identity     | Obsahuje seznam deklarací identity, které mají být přítomny na tokenu, aby se dalo považovat za platný. Pokud `match` je atribut nastavený na `all` hodnotu každá hodnota deklarace v zásadě, musí být v tokenu přítomná, aby bylo ověření úspěšné. Pokud `match` je atribut nastaven na `any` alespoň jednu deklaraci identity, musí být v tokenu přítomen, aby bylo ověření úspěšné. | Ne       |

### <a name="attributes"></a>Atributy

| Název                            | Popis                                                                                                                                                                                                                                                                                                                                                                                                                                            | Povinné                                                                         | Výchozí                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| hodiny – zkosit                      | TimeSpan. Slouží k zadání maximálního očekávaného časového rozdílu mezi systémovými hodinami vystavitele tokenu a instancí API Management.                                                                                                                                                                                                                                                                                                               | Ne                                                                               | 0 sekund                                                                         |
| Chyba-ověření-zpráva | Chybová zpráva, která se má vrátit v těle odpovědi HTTP, pokud metoda JWT neprojde ověřením. Tato zpráva musí mít správně uvozené speciální znaky.                                                                                                                                                                                                                                                                                                 | Ne                                                                               | Výchozí chybová zpráva závisí na potížích s ověřením, například "JWT není k dispozici". |
| Chyba-ověření – httpCode      | Stavový kód HTTP, který se má vrátit, pokud metoda JWT neprojde ověřením.                                                                                                                                                                                                                                                                                                                                                                                         | Ne                                                                               | 401                                                                               |
| záhlaví – název                     | Název hlavičky protokolu HTTP, která drží token.                                                                                                                                                                                                                                                                                                                                                                                                         | `header-name` `query-parameter-name` Je nutné zadat jeden z těchto nebo `token-value` . | –                                                                               |
| dotaz-parametr-název            | Název parametru dotazu, který drží token.                                                                                                                                                                                                                                                                                                                                                                                                     | `header-name` `query-parameter-name` Je nutné zadat jeden z těchto nebo `token-value` . | –                                                                               |
| hodnota tokenu                     | Výraz vracející řetězec obsahující token JWT                                                                                                                                                                                                                                                                                                                                                                                                     | `header-name` `query-parameter-name` Je nutné zadat jeden z těchto nebo `token-value` . | –                                                                               |
| id                              | `id`Atribut `key` elementu umožňuje zadat řetězec, který se bude shodovat s `kid` deklarací identity v tokenu (Pokud je k dispozici) a zjistit odpovídající klíč pro ověření podpisu.                                                                                                                                                                                                                                           | Ne                                                                               | –                                                                               |
| match                           | `match`Atribut `claim` elementu určuje, zda musí být v tokenu přítomna každá hodnota deklarace identity, aby bylo ověření úspěšné. Možné hodnoty:<br /><br /> - `all` -v tokenu musí být k dispozici všechny hodnoty deklarací v zásadě, aby bylo ověření úspěšné.<br /><br /> - `any` -v tokenu musí být k dispozici alespoň jedna hodnota deklarace, aby bylo ověření úspěšné.                                                       | Ne                                                                               | Vše                                                                               |
| vyžadovat – čas vypršení platnosti         | Datového. Určuje, jestli je v tokenu vyžadována deklarace identity vypršení platnosti.                                                                                                                                                                                                                                                                                                                                                                               | Ne                                                                               | true                                                                              |
| vyžadovat – schéma                  | Název schématu tokenu, např. "nosič". Pokud je tento atribut nastavený, zásada ověří, jestli je v hodnotě autorizační hlavičky zadané schéma.                                                                                                                                                                                                                                                                                    | Ne                                                                               | –                                                                               |
| vyžadovat – podepsané tokeny           | Datového. Určuje, zda je vyžadován token, který má být podepsán.                                                                                                                                                                                                                                                                                                                                                                                           | Ne                                                                               | true                                                                              |
| oddělování                       | Řetězec. Určuje oddělovač (například ","), který se použije k extrakci sady hodnot z deklarace s více hodnotami.                                                                                                                                                                                                                                                                                                                                          | Ne                                                                               | –                                                                               |
| url                             | Otevřete adresu URL koncového bodu konfigurace ID, ze kterého lze získat metadata konfigurace Open ID. Odpověď by měla být podle specifikací definovaných na adrese URL: `https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata` . Pro Azure Active Directory použijte následující adresu URL: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` nahrazení názvu tenanta adresáře, např. `contoso.onmicrosoft.com` . | Ano                                                                              | –                                                                               |
| výstup-token-proměnná-Name      | Řetězec. Název kontextové proměnné, která obdrží hodnotu tokenu jako objekt typu [`Jwt`](api-management-policy-expressions.md) po úspěšném ověření tokenu                                                                                                                                                                                                                                                                                     | Ne                                                                               | –                                                                               |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí
-   **Obory zásad:** všechny rozsahy

## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami najdete v těchto tématech:

-   [Zásady v API Management](api-management-howto-policies.md)
-   [Transformační rozhraní API](transform-api.md)
-   [Odkaz na zásady](./api-management-policies.md) pro úplný seznam příkazů zásad a jejich nastavení
-   [Ukázky zásad](./policy-reference.md)
