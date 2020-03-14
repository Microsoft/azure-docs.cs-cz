---
title: Zásady ukládání do mezipaměti pro Azure API Management | Microsoft Docs
description: Přečtěte si o zásadách ukládání do mezipaměti, které jsou dostupné pro použití v Azure API Management.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280297"
---
# <a name="api-management-caching-policies"></a>Zásady ukládání do mezipaměti API Management
V tomto tématu najdete referenční informace pro následující zásady API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady v API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CachingPolicies"></a>Zásady ukládání do mezipaměti

- Zásady ukládání odpovědí do mezipaměti
    - [Získat z mezipaměti](api-management-caching-policies.md#GetFromCache) – vyhledá mezipaměť a vrátí platné odpovědi v mezipaměti, pokud jsou k dispozici.
    - [Ukládat do mezipaměti](api-management-caching-policies.md#StoreToCache) odpovědi v závislosti na zadané konfiguraci řízení mezipaměti.
- Zásady ukládání hodnot do mezipaměti
    - [Získat hodnotu z mezipaměti](#GetFromCacheByKey) – načte položku uloženou v mezipaměti podle klíče.
    - [Uložit hodnotu v mezipaměti](#StoreToCacheByKey) – uložit položku do mezipaměti podle klíče
    - [Odebrat hodnotu z mezipaměti](#RemoveCacheByKey) – odebere položku v mezipaměti podle klíče.

## <a name="GetFromCache"></a>Získat z mezipaměti
Pomocí zásad `cache-lookup` proveďte hledání v mezipaměti a v případě dostupnosti vraťte platnou odpověď uloženou v mezipaměti. Tato zásada se dá použít v případech, kdy obsah odpovědi zůstane v časovém intervalu statický. Ukládání odpovědí do mezipaměti omezuje požadavky na šířku pásma a zpracování na back-end webovém serveru a snižuje latenci zjištěné uživateli rozhraní API.

> [!NOTE]
> Tato zásada musí mít odpovídající [úložiště pro zásady ukládání do mezipaměti](api-management-caching-policies.md#StoreToCache) .

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
V tomto příkladu se dozvíte, jak nakonfigurovat API Management dobu ukládání odpovědí do mezipaměti, která odpovídá mezipaměti služby back-end, jak je uvedeno v direktivě `Cache-Control` zálohované služby. Ukázku konfigurace a používání těchto zásad najdete v tématu [cloudový krytový díl 177: další API Management funkce s využitím Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) a rychlé převinutí na 25:25.

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

Další informace najdete v tématu [výrazy zásad](api-management-policy-expressions.md) a [kontextová proměnná](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementy

|Název|Popis|Požadováno|
|----------|-----------------|--------------|
|mezipaměť – vyhledávání|Kořenový element.|Ano|
|různé – podle záhlaví|Zahájit ukládání odpovědí do mezipaměti na hodnotu zadaného záhlaví, například přijmout, přijmout-charset, přijmout – kódování, přijmout – jazyk, autorizace, očekávané, z, hostitel, If-Match.|Ne|
|různé – podle dotazu – parametr|Spustí ukládání odpovědí do mezipaměti na hodnotu zadaných parametrů dotazu. Zadejte jeden nebo více parametrů. Jako oddělovač použijte středník. Pokud není zadán žádný parametr, jsou použity všechny parametry dotazu.|Ne|

### <a name="attributes"></a>Atributy

| Název                           | Popis                                                                                                                                                                                                                                                                                                                                                 | Požadováno | Výchozí           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| allow-private-response-caching | Když nastavíte `true`, povolí ukládání požadavků, které obsahují autorizační hlavičku.                                                                                                                                                                                                                                                                        | Ne       | false             |
| typ ukládání do mezipaměti               | Vyberte jednu z následujících hodnot atributu:<br />- `internal` používat integrovanou API Management mezipaměť,<br />- `external` použít externí mezipaměť, jak je popsáno v tématu [použití externí mezipaměti Azure pro Redis ve službě Azure API Management](api-management-howto-cache-external.md),<br />v opačném případě se - `prefer-external` použít externí mezipaměť, pokud je nakonfigurovaná nebo interní mezipaměť. | Ne       | `prefer-external` |
| downstream-caching-type        | Tento atribut musí být nastaven na jednu z následujících hodnot.<br /><br /> -None-ukládání do mezipaměti se nepovoluje.<br />– soukromé ukládání do mezipaměti privátních dat je povolené.<br />– ukládání do mezipaměti veřejného a sdíleného pro příjem dat je povolené.                                                                                                          | Ne       | Žádná              |
| nutné – znovu ověřit                | Pokud je povoleno ukládání do mezipaměti, tato vlastnost zapne nebo vypne direktivu řízení mezipaměti `must-revalidate` v odpovědích brány.                                                                                                                                                                                                                      | Ne       | true              |
| různé – podle vývojáře              | Nastavte na `true` pro ukládání odpovědí do mezipaměti na [klíč předplatného](https://docs.microsoft.com/azure/api-management/api-management-subscriptions).                                                                                                                                                                                                                                                                                                         | Ano      |         Nepravda          |
| vary-by-developer-groups       | Nastavte na `true` pro ukládání odpovědí do mezipaměti na [skupinu uživatelů](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups).                                                                                                                                                                                                                                                                                                             | Ano      |       Nepravda            |

### <a name="usage"></a>Využití
Tyto zásady se dají použít v následujících [oddílech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zásad.

- **Oddíly zásad:** příchozí
- **Obory zásad:** všechny rozsahy

## <a name="StoreToCache"></a>Ukládat do mezipaměti
Zásada `cache-store` ukládá odpovědi do mezipaměti podle zadaného nastavení mezipaměti. Tato zásada se dá použít v případech, kdy obsah odpovědi zůstane v časovém intervalu statický. Ukládání odpovědí do mezipaměti omezuje požadavky na šířku pásma a zpracování na back-end webovém serveru a snižuje latenci zjištěné uživateli rozhraní API.

> [!NOTE]
> Tato zásada musí mít odpovídající možnost [získat ze zásad mezipaměti](api-management-caching-policies.md#GetFromCache) .

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
V tomto příkladu se dozvíte, jak nakonfigurovat API Management dobu ukládání odpovědí do mezipaměti, která odpovídá mezipaměti služby back-end, jak je uvedeno v direktivě `Cache-Control` zálohované služby. Ukázku konfigurace a používání těchto zásad najdete v tématu [cloudový krytový díl 177: další API Management funkce s využitím Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) a rychlé převinutí na 25:25.

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

Další informace najdete v tématu [výrazy zásad](api-management-policy-expressions.md) a [kontextová proměnná](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementy

|Název|Popis|Požadováno|
|----------|-----------------|--------------|
|ukládat do mezipaměti|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

| Název             | Popis                                                                                                                                                                                                                                                                                                                                                 | Požadováno | Výchozí           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| duration         | Doba až živých položek v mezipaměti, které jsou zadány v sekundách.                                                                                                                                                                                                                                                                                                   | Ano      | NEUŽÍVÁ SE.               |

### <a name="usage"></a>Využití
Tyto zásady se dají použít v následujících [oddílech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zásad.

- **Oddíly zásad:** odchozí
- **Obory zásad:** všechny rozsahy

## <a name="GetFromCacheByKey"></a>Získat hodnotu z mezipaměti
Pomocí zásad `cache-lookup-value` proveďte vyhledávání v mezipaměti podle klíče a vraťte hodnotu uloženou v mezipaměti. Klíč může obsahovat libovolnou řetězcovou hodnotu a obvykle se poskytuje pomocí výrazu zásad.

> [!NOTE]
> Tato zásada musí mít odpovídající [hodnotu úložiště v zásadách mezipaměti](#StoreToCacheByKey) .

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Příklad
Další informace a příklady těchto zásad najdete v tématu [vlastní ukládání do mezipaměti v Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Elementy

|Název|Popis|Požadováno|
|----------|-----------------|--------------|
|cache-lookup-value|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

| Název             | Popis                                                                                                                                                                                                                                                                                                                                                 | Požadováno | Výchozí           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| typ ukládání do mezipaměti | Vyberte jednu z následujících hodnot atributu:<br />- `internal` používat integrovanou API Management mezipaměť,<br />- `external` použít externí mezipaměť, jak je popsáno v tématu [použití externí mezipaměti Azure pro Redis ve službě Azure API Management](api-management-howto-cache-external.md),<br />v opačném případě se - `prefer-external` použít externí mezipaměť, pokud je nakonfigurovaná nebo interní mezipaměť. | Ne       | `prefer-external` |
| Výchozí hodnota    | Hodnota, která bude přiřazena proměnné v případě, že výsledkem hledání klíče mezipaměti bylo neúspěšné. Pokud tento atribut není zadán, je přiřazena `null`.                                                                                                                                                                                                           | Ne       | `null`            |
| key              | Hodnota klíče mezipaměti, která se má použít při vyhledávání                                                                                                                                                                                                                                                                                                                       | Ano      | NEUŽÍVÁ SE.               |
| název proměnné    | Název [kontextové proměnné](api-management-policy-expressions.md#ContextVariables) , ke které je hledaná hodnota přiřazena, pokud vyhledávání proběhlo úspěšně. Pokud dojde k neúspěšnému vyhledávání, proměnná se přiřadí hodnotě atributu `default-value` nebo `null`, pokud je atribut `default-value` vynechán.                                       | Ano      | NEUŽÍVÁ SE.               |

### <a name="usage"></a>Využití
Tyto zásady se dají použít v následujících [oddílech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zásad.

- **Oddíly zásad:** příchozí, odchozí, back-end, zapnutá chyba
- **Obory zásad:** všechny rozsahy

## <a name="StoreToCacheByKey"></a>Hodnota úložiště v mezipaměti
`cache-store-value` provádí ukládání do mezipaměti podle klíče. Klíč může obsahovat libovolnou řetězcovou hodnotu a obvykle se poskytuje pomocí výrazu zásad.

> [!NOTE]
> Tato zásada musí mít odpovídající [hodnotu Get ze zásad mezipaměti](#GetFromCacheByKey) .

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Příklad
Další informace a příklady těchto zásad najdete v tématu [vlastní ukládání do mezipaměti v Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Elementy

|Název|Popis|Požadováno|
|----------|-----------------|--------------|
|cache-store-value|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

| Název             | Popis                                                                                                                                                                                                                                                                                                                                                 | Požadováno | Výchozí           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| typ ukládání do mezipaměti | Vyberte jednu z následujících hodnot atributu:<br />- `internal` používat integrovanou API Management mezipaměť,<br />- `external` použít externí mezipaměť, jak je popsáno v tématu [použití externí mezipaměti Azure pro Redis ve službě Azure API Management](api-management-howto-cache-external.md),<br />v opačném případě se - `prefer-external` použít externí mezipaměť, pokud je nakonfigurovaná nebo interní mezipaměť. | Ne       | `prefer-external` |
| duration         | Hodnota se uloží do mezipaměti pro zadanou hodnotu trvání zadanou v sekundách.                                                                                                                                                                                                                                                                                 | Ano      | NEUŽÍVÁ SE.               |
| key              | Klíč mezipaměti, do které se hodnota uloží.                                                                                                                                                                                                                                                                                                                   | Ano      | NEUŽÍVÁ SE.               |
| value            | Hodnota, která má být uložena do mezipaměti.                                                                                                                                                                                                                                                                                                                                     | Ano      | NEUŽÍVÁ SE.               |
### <a name="usage"></a>Využití
Tyto zásady se dají použít v následujících [oddílech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zásad.

- **Oddíly zásad:** příchozí, odchozí, back-end, zapnutá chyba
- **Obory zásad:** všechny rozsahy

### <a name="RemoveCacheByKey"></a>Odebrat hodnotu z mezipaměti
`cache-remove-value` odstraní položku uloženou v mezipaměti, kterou identifikuje její klíč. Klíč může obsahovat libovolnou řetězcovou hodnotu a obvykle se poskytuje pomocí výrazu zásad.

#### <a name="policy-statement"></a>Prohlášení o zásadách

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Příklad

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Elementy

|Název|Popis|Požadováno|
|----------|-----------------|--------------|
|cache-remove-value|Kořenový element.|Ano|

#### <a name="attributes"></a>Atributy

| Název             | Popis                                                                                                                                                                                                                                                                                                                                                 | Požadováno | Výchozí           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| typ ukládání do mezipaměti | Vyberte jednu z následujících hodnot atributu:<br />- `internal` používat integrovanou API Management mezipaměť,<br />- `external` použít externí mezipaměť, jak je popsáno v tématu [použití externí mezipaměti Azure pro Redis ve službě Azure API Management](api-management-howto-cache-external.md),<br />v opačném případě se - `prefer-external` použít externí mezipaměť, pokud je nakonfigurovaná nebo interní mezipaměť. | Ne       | `prefer-external` |
| key              | Klíč hodnoty dříve uložené v mezipaměti, který má být odstraněn z mezipaměti.                                                                                                                                                                                                                                                                                        | Ano      | NEUŽÍVÁ SE.               |

#### <a name="usage"></a>Využití
Tyto zásady se dají použít v následujících [oddílech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) zásad.

- **Oddíly zásad:** příchozí, odchozí, back-end, zapnutá chyba
- **Obory zásad:** všechny rozsahy

## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami najdete v těchto tématech:

+ [Zásady v API Management](api-management-howto-policies.md)
+ [Transformační rozhraní API](transform-api.md)
+ [Odkaz na zásady](api-management-policy-reference.md) pro úplný seznam příkazů zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)
