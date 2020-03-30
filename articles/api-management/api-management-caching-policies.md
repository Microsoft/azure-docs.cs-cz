---
title: Zásady ukládání do mezipaměti služby Azure API Management | Dokumenty společnosti Microsoft
description: Přečtěte si o zásadách ukládání do mezipaměti, které jsou k dispozici pro použití ve správě rozhraní Azure API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 06c4ede12f939e48973d3e0b502d90b848d199bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280297"
---
# <a name="api-management-caching-policies"></a>Zásady ukládání do mezipaměti pro službu API Management
Toto téma obsahuje odkaz na následující zásady správy rozhraní API. Informace o přidávání a konfiguraci zásad naleznete [v tématu Zásady ve správě rozhraní API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="caching-policies"></a><a name="CachingPolicies"></a>Zásady ukládání do mezipaměti

- Zásady ukládání odpovědí do mezipaměti
    - [Získat z mezipaměti](api-management-caching-policies.md#GetFromCache) – Proveďte vyhledávání mezipaměti a vraťte platné odpovědi uložené v mezipaměti, pokud jsou k dispozici.
    - [Uložit do mezipaměti](api-management-caching-policies.md#StoreToCache) - Ukládá odpovědi podle zadané konfigurace ovládacího prvku mezipaměti.
- Zásady ukládání hodnot do mezipaměti
    - [Získat hodnotu z mezipaměti](#GetFromCacheByKey) – načtení položky uložené v mezipaměti pomocí klíče.
    - [Ukládat hodnotu do mezipaměti](#StoreToCacheByKey) – položku do mezipaměti uložte podle klíče.
    - [Odebrat hodnotu z mezipaměti](#RemoveCacheByKey) – Odebere položku v mezipaměti pomocí klíče.

## <a name="get-from-cache"></a><a name="GetFromCache"></a>Získat z mezipaměti
Pomocí `cache-lookup` této zásady můžete provést vyhledávání mezipaměti a vrátit platnou odpověď uloženou v mezipaměti, pokud je k dispozici. Tuto zásadu lze použít v případech, kdy obsah odpovědi zůstává statický po určitou dobu. Ukládání odpovědí do mezipaměti snižuje šířku pásma a požadavky na zpracování uložené na back-endovém webovém serveru a snižuje latenci vnímanou spotřebiteli rozhraní API.

> [!NOTE]
> Tato zásada musí mít odpovídající [zásady úložiště pro ukládání do mezipaměti.](api-management-caching-policies.md#StoreToCache)

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" caching-type="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
  <vary-by-header>Accept</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Accept-Charset</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Authorization</vary-by-header>
  <!-- should be present when allow-private-response-caching is "true"-->
  <vary-by-header>header name</vary-by-header>
  <!-- optional, can repeated several times -->
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>
  <!-- optional, can repeated several times -->
</cache-lookup>
```

### <a name="examples"></a>Příklady

#### <a name="example"></a>Příklad

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" caching-type="internal" >
            <vary-by-query-parameter>version</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="seconds" />
        <base />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Příklad použití výrazů zásad
Tento příklad ukazuje, jak nakonfigurovat dobu ukládání do mezipaměti odpovědi správy rozhraní API, která odpovídá `Cache-Control` ukládání odpovědi do mezipaměti back-endové služby, jak je určeno směrnicí zálohované služby. Ukázka konfigurace a používání těchto zásad najdete v [tématu Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) and fast-forward to 25:25.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Další informace naleznete [v tématu Výrazy zásad](api-management-policy-expressions.md) a [kontextová proměnná](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementy

|Name (Název)|Popis|Požaduje se|
|----------|-----------------|--------------|
|vyhledávání mezipaměti|Kořenový prvek.|Ano|
|lišit podle záhlaví|Spusťte ukládání odpovědí do mezipaměti podle hodnoty zadané hlavičky, například Accept, Accept-Charset, Accept-Encoding, Accept-Language, Authorization, Expect, From, Host, If-Match.|Ne|
|vary-by-query-parametr|Spusťte ukládání odpovědí do mezipaměti podle hodnoty zadaných parametrů dotazu. Zadejte jeden nebo více parametrů. Jako oddělovač použijte středník. Pokud nejsou zadány žádné, jsou použity všechny parametry dotazu.|Ne|

### <a name="attributes"></a>Atributy

| Name (Název)                           | Popis                                                                                                                                                                                                                                                                                                                                                 | Požaduje se | Výchozí           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| povolit ukládání do mezipaměti soukromé odpovědi | Pokud je `true`nastavena na , umožňuje ukládání požadavků do mezipaměti, které obsahují hlavičku autorizace.                                                                                                                                                                                                                                                                        | Ne       | false (nepravda)             |
| typ ukládání do mezipaměti               | Vyberte si mezi následujícími hodnotami atributu:<br />- `internal`použití integrované mezipaměti správy rozhraní API,<br />- `external`použití externí mezipaměti, jak je popsáno v [části Použití externí mezipaměti Azure pro Redis ve správě rozhraní API Azure](api-management-howto-cache-external.md),<br />- `prefer-external`chcete-li použít externí mezipaměť, pokud je nakonfigurována, nebo interní mezipaměť jinak. | Ne       | `prefer-external` |
| typu následného ukládání do mezipaměti        | Tento atribut musí být nastaven na jednu z následujících hodnot.<br /><br /> - žádný - následné ukládání do mezipaměti není povoleno.<br />- soukromé - po proudu soukromé ukládání do mezipaměti je povoleno.<br />- veřejné - soukromé a sdílené ukládání do mezipaměti navazujícím trhu je povoleno.                                                                                                          | Ne       | Žádná              |
| musí být obnovena                | Pokud je povoleno ukládání do mezipaměti pro `must-revalidate` příjem dat, tento atribut zapne nebo vypne direktivu řízení mezipaměti v odpovědích brány.                                                                                                                                                                                                                      | Ne       | true              |
| lišit podle vývojáře              | Nastavte `true` na do mezipaměti odpovědi na [klíč odběru](https://docs.microsoft.com/azure/api-management/api-management-subscriptions).                                                                                                                                                                                                                                                                                                         | Ano      |         False          |
| liší se podle skupin pro vývojáře       | Nastavte `true` na do mezipaměti odpovědi podle [skupiny uživatelů](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups).                                                                                                                                                                                                                                                                                                             | Ano      |       False            |

### <a name="usage"></a>Využití
Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Oddíly zásad:** příchozí
- **Obory zásad:** všechny obory

## <a name="store-to-cache"></a><a name="StoreToCache"></a>Uložit do mezipaměti
Zásady `cache-store` ukládat odpovědi do mezipaměti podle zadaného nastavení mezipaměti. Tuto zásadu lze použít v případech, kdy obsah odpovědi zůstává statický po určitou dobu. Ukládání odpovědí do mezipaměti snižuje šířku pásma a požadavky na zpracování uložené na back-endovém webovém serveru a snižuje latenci vnímanou spotřebiteli rozhraní API.

> [!NOTE]
> Tato zásada musí mít odpovídající [zásady Get from cache.](api-management-caching-policies.md#GetFromCache)

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<cache-store duration="seconds" />
```

### <a name="examples"></a>Příklady

#### <a name="example"></a>Příklad

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">
            <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
        </cache-lookup>
    </inbound>
    <outbound>
        <base />
        <cache-store duration="3600" />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Příklad použití výrazů zásad
Tento příklad ukazuje, jak nakonfigurovat dobu ukládání do mezipaměti odpovědi správy rozhraní API, která odpovídá `Cache-Control` ukládání odpovědi do mezipaměti back-endové služby, jak je určeno směrnicí zálohované služby. Ukázka konfigurace a používání těchto zásad najdete v [tématu Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) and fast-forward to 25:25.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Další informace naleznete [v tématu Výrazy zásad](api-management-policy-expressions.md) a [kontextová proměnná](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementy

|Name (Název)|Popis|Požaduje se|
|----------|-----------------|--------------|
|cache-store|Kořenový prvek.|Ano|

### <a name="attributes"></a>Atributy

| Name (Název)             | Popis                                                                                                                                                                                                                                                                                                                                                 | Požaduje se | Výchozí           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| doba trvání         | Doba aktivní od položek uložených v mezipaměti zadaná v sekundách.                                                                                                                                                                                                                                                                                                   | Ano      | Není dostupné.               |

### <a name="usage"></a>Využití
Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Oddíly zásad:** odchozí
- **Obory zásad:** všechny obory

## <a name="get-value-from-cache"></a><a name="GetFromCacheByKey"></a>Získat hodnotu z mezipaměti
Pomocí `cache-lookup-value` zásady můžete provádět vyhledávání mezipaměti podle klíče a vracet hodnotu uloženou v mezipaměti. Klíč může mít libovolnou řetězcovou hodnotu a je obvykle k dispozici pomocí výrazu zásady.

> [!NOTE]
> Tato zásada musí mít odpovídající hodnotu úložiště v zásadách [mezipaměti.](#StoreToCacheByKey)

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Příklad
Další informace a příklady této zásady najdete [v tématu Vlastní ukládání do mezipaměti ve správě rozhraní Azure API](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Elementy

|Name (Název)|Popis|Požaduje se|
|----------|-----------------|--------------|
|hodnota vyhledávání mezipaměti|Kořenový prvek.|Ano|

### <a name="attributes"></a>Atributy

| Name (Název)             | Popis                                                                                                                                                                                                                                                                                                                                                 | Požaduje se | Výchozí           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| typ ukládání do mezipaměti | Vyberte si mezi následujícími hodnotami atributu:<br />- `internal`použití integrované mezipaměti správy rozhraní API,<br />- `external`použití externí mezipaměti, jak je popsáno v [části Použití externí mezipaměti Azure pro Redis ve správě rozhraní API Azure](api-management-howto-cache-external.md),<br />- `prefer-external`chcete-li použít externí mezipaměť, pokud je nakonfigurována, nebo interní mezipaměť jinak. | Ne       | `prefer-external` |
| výchozí hodnota    | Hodnota, která bude přiřazena proměnné, pokud vyhledávání klíče mezipaměti vedlo k chybě. Pokud tento atribut není `null` zadán, je přiřazen.                                                                                                                                                                                                           | Ne       | `null`            |
| key              | Hodnota klíče mezipaměti, která se má použít při vyhledávání.                                                                                                                                                                                                                                                                                                                       | Ano      | Není dostupné.               |
| název proměnné    | Název [kontextové proměnné,](api-management-policy-expressions.md#ContextVariables) ke které bude přiřazena hodnota vyhledávání, pokud je vyhledávání úspěšné. Pokud výsledkem vyhledávání je nečeká, bude proměnné `default-value` přiřazena `null`hodnota `default-value` atributu nebo , pokud je atribut vynechán.                                       | Ano      | Není dostupné.               |

### <a name="usage"></a>Využití
Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Oddíly zásad:** příchozí, odchozí, back-end, při chybě
- **Obory zásad:** všechny obory

## <a name="store-value-in-cache"></a><a name="StoreToCacheByKey"></a>Uložit hodnotu do mezipaměti
Provádí `cache-store-value` ukládání mezipaměti podle klíče. Klíč může mít libovolnou řetězcovou hodnotu a je obvykle k dispozici pomocí výrazu zásady.

> [!NOTE]
> Tato zásada musí mít odpovídající [Get hodnotu z mezipaměti](#GetFromCacheByKey) zásady.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Příklad
Další informace a příklady této zásady najdete [v tématu Vlastní ukládání do mezipaměti ve správě rozhraní Azure API](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Elementy

|Name (Název)|Popis|Požaduje se|
|----------|-----------------|--------------|
|hodnota mezipaměti-úložiště|Kořenový prvek.|Ano|

### <a name="attributes"></a>Atributy

| Name (Název)             | Popis                                                                                                                                                                                                                                                                                                                                                 | Požaduje se | Výchozí           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| typ ukládání do mezipaměti | Vyberte si mezi následujícími hodnotami atributu:<br />- `internal`použití integrované mezipaměti správy rozhraní API,<br />- `external`použití externí mezipaměti, jak je popsáno v [části Použití externí mezipaměti Azure pro Redis ve správě rozhraní API Azure](api-management-howto-cache-external.md),<br />- `prefer-external`chcete-li použít externí mezipaměť, pokud je nakonfigurována, nebo interní mezipaměť jinak. | Ne       | `prefer-external` |
| doba trvání         | Hodnota bude uložena do mezipaměti pro zadanou hodnotu doby trvání zadanou v sekundách.                                                                                                                                                                                                                                                                                 | Ano      | Není dostupné.               |
| key              | Klíč mezipaměti, pod kterými bude hodnota uložena.                                                                                                                                                                                                                                                                                                                   | Ano      | Není dostupné.               |
| value            | Hodnota, která má být uložena do mezipaměti.                                                                                                                                                                                                                                                                                                                                     | Ano      | Není dostupné.               |
### <a name="usage"></a>Využití
Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Oddíly zásad:** příchozí, odchozí, back-end, při chybě
- **Obory zásad:** všechny obory

### <a name="remove-value-from-cache"></a><a name="RemoveCacheByKey"></a>Odebrat hodnotu z mezipaměti
Odstraní `cache-remove-value` položku uloženou v mezipaměti identifikovanou jejím klíčem. Klíč může mít libovolnou řetězcovou hodnotu a je obvykle k dispozici pomocí výrazu zásady.

#### <a name="policy-statement"></a>Prohlášení o zásadách

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Příklad

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Elementy

|Name (Název)|Popis|Požaduje se|
|----------|-----------------|--------------|
|hodnota odebrání mezipaměti|Kořenový prvek.|Ano|

#### <a name="attributes"></a>Atributy

| Name (Název)             | Popis                                                                                                                                                                                                                                                                                                                                                 | Požaduje se | Výchozí           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| typ ukládání do mezipaměti | Vyberte si mezi následujícími hodnotami atributu:<br />- `internal`použití integrované mezipaměti správy rozhraní API,<br />- `external`použití externí mezipaměti, jak je popsáno v [části Použití externí mezipaměti Azure pro Redis ve správě rozhraní API Azure](api-management-howto-cache-external.md),<br />- `prefer-external`chcete-li použít externí mezipaměť, pokud je nakonfigurována, nebo interní mezipaměť jinak. | Ne       | `prefer-external` |
| key              | Klíč dříve uložené hodnoty v mezipaměti, která má být odebrána z mezipaměti.                                                                                                                                                                                                                                                                                        | Ano      | Není dostupné.               |

#### <a name="usage"></a>Využití
Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .

- **Oddíly zásad:** příchozí, odchozí, back-end, při chybě
- **Obory zásad:** všechny obory

## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami naleznete v tématu:

+ [Zásady ve správě rozhraní API](api-management-howto-policies.md)
+ [Transformovat api](transform-api.md)
+ [Odkaz na zásady](api-management-policy-reference.md) pro úplný seznam prohlášení zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)
