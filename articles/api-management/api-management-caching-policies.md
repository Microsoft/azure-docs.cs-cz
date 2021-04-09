---
title: Zásady ukládání do mezipaměti pro Azure API Management | Microsoft Docs
description: Přečtěte si o zásadách ukládání do mezipaměti, které jsou dostupné pro použití v Azure API Management. Podívejte se na příklady a zobrazte další dostupné prostředky.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 03/08/2021
ms.author: apimpm
ms.openlocfilehash: 9888627bed0fbf90abc75c81564dacc0d1aac18e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103233462"
---
# <a name="api-management-caching-policies"></a>Zásady ukládání do mezipaměti pro službu API Management
V tomto tématu najdete referenční informace pro následující zásady API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady v API Management](./api-management-policies.md).

> [!IMPORTANT]
> Integrovaná mezipaměť je nestálá a sdílí se všemi jednotkami ve stejné oblasti ve stejné API Management službě.

## <a name="caching-policies"></a><a name="CachingPolicies"></a> Zásady ukládání do mezipaměti

- Zásady ukládání odpovědí do mezipaměti
    - [Získat z mezipaměti](#GetFromCache) – vyhledá mezipaměť a vrátí platné odpovědi v mezipaměti, pokud jsou k dispozici.
    - [Ukládat do mezipaměti](#StoreToCache) odpovědi v závislosti na zadané konfiguraci řízení mezipaměti.
- Zásady ukládání hodnot do mezipaměti
    - [Získat hodnotu z mezipaměti](#GetFromCacheByKey) – načte položku uloženou v mezipaměti podle klíče.
    - [Uložit hodnotu v mezipaměti](#StoreToCacheByKey) – uložit položku do mezipaměti podle klíče
    - [Odebrat hodnotu z mezipaměti](#RemoveCacheByKey) – odebere položku v mezipaměti podle klíče.

## <a name="get-from-cache"></a><a name="GetFromCache"></a> Získat z mezipaměti
Pomocí `cache-lookup` zásady můžete vyhledat mezipaměť a vrátit platnou odpověď uloženou v mezipaměti, pokud je dostupná. Tato zásada se dá použít v případech, kdy obsah odpovědi zůstane v časovém intervalu statický. Ukládání odpovědí do mezipaměti omezuje požadavky na šířku pásma a zpracování na back-end webovém serveru a snižuje latenci zjištěné uživateli rozhraní API.

> [!NOTE]
> Tato zásada musí mít odpovídající [úložiště pro zásady ukládání do mezipaměti](#StoreToCache) .

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
Tento příklad ukazuje, jak nakonfigurovat API Management dobu ukládání odpovědí do mezipaměti, která odpovídá mezipaměti služby back-end, jak je uvedeno v direktivě zálohované služby `Cache-Control` . Ukázku konfigurace a používání těchto zásad najdete v tématu [cloudový krytový díl 177: další API Management funkce s využitím Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) a rychlé převinutí na 25:25.

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

|Název|Popis|Povinné|
|----------|-----------------|--------------|
|mezipaměť – vyhledávání|Kořenový element.|Yes|
|různé – podle záhlaví|Zahájit ukládání odpovědí do mezipaměti na hodnotu zadaného záhlaví, například přijmout, přijmout-charset, přijmout – kódování, přijmout – jazyk, autorizace, očekávané, z, hostitel, If-Match.|No|
|různé – podle dotazu – parametr|Spustí ukládání odpovědí do mezipaměti na hodnotu zadaných parametrů dotazu. Zadejte jeden nebo více parametrů. Jako oddělovač použijte středník. Pokud není zadán žádný parametr, jsou použity všechny parametry dotazu.|No|

### <a name="attributes"></a>Atributy

| Název                           | Popis                                                                                                                                                                                                                                                                                                                                                 | Povinné | Výchozí           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| povolení ukládání privátních odpovědí do mezipaměti | Pokud je nastaveno na `true` , umožňuje ukládání požadavků, které obsahují autorizační hlavičku, do mezipaměti.                                                                                                                                                                                                                                                                        | No       | false (nepravda)             |
| typ ukládání do mezipaměti               | Vyberte jednu z následujících hodnot atributu:<br />- `internal` Pokud chcete používat integrovanou API Management mezipaměť,<br />- `external` Pokud chcete použít externí mezipaměť, jak je popsáno v tématu [použití externí mezipaměti Azure pro Redis v Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` Pokud chcete použít externí mezipaměť v případě, že je nakonfigurovaná nebo interní mezipaměť, jinak. | No       | `prefer-external` |
| pro ukládání do mezipaměti – typ        | Tento atribut musí být nastaven na jednu z následujících hodnot.<br /><br /> -None-ukládání do mezipaměti se nepovoluje.<br />– soukromé ukládání do mezipaměti privátních dat je povolené.<br />– ukládání do mezipaměti veřejného a sdíleného pro příjem dat je povolené.                                                                                                          | No       | žádné              |
| nutné – znovu ověřit                | Pokud je povoleno ukládání do mezipaměti, tato vlastnost zapne nebo vypne `must-revalidate` direktivu řízení mezipaměti v odpovědích brány.                                                                                                                                                                                                                      | No       | true              |
| různé – podle vývojáře              | Nastavte na `true` do mezipaměti odpovědi na účet vývojáře, který vlastní [klíč předplatného](./api-management-subscriptions.md) , který je součástí žádosti.                                                                                                                                                                                                                                                                                                  | Yes      |         Ne          |
| různé – podle – Developer-Groups       | Nastavte na `true` pro ukládání odpovědí do mezipaměti na [skupinu uživatelů](./api-management-howto-create-groups.md).                                                                                                                                                                                                                                                                                                             | Yes      |       Ne            |

### <a name="usage"></a>Využití
Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

- **Oddíly zásad:** příchozí
- **Obory zásad:** všechny rozsahy

## <a name="store-to-cache"></a><a name="StoreToCache"></a> Ukládat do mezipaměti
`cache-store`Zásada ukládá odpovědi do mezipaměti podle zadaného nastavení mezipaměti. Tato zásada se dá použít v případech, kdy obsah odpovědi zůstane v časovém intervalu statický. Ukládání odpovědí do mezipaměti omezuje požadavky na šířku pásma a zpracování na back-end webovém serveru a snižuje latenci zjištěné uživateli rozhraní API.

> [!NOTE]
> Tato zásada musí mít odpovídající možnost [získat ze zásad mezipaměti](api-management-caching-policies.md#GetFromCache) .

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<cache-store duration="seconds" cache-response="true | false" />
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
Tento příklad ukazuje, jak nakonfigurovat API Management dobu ukládání odpovědí do mezipaměti, která odpovídá mezipaměti služby back-end, jak je uvedeno v direktivě zálohované služby `Cache-Control` . Ukázku konfigurace a používání těchto zásad najdete v tématu [cloudový krytový díl 177: další API Management funkce s využitím Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) a rychlé převinutí na 25:25.

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

|Název|Popis|Povinné|
|----------|-----------------|--------------|
|ukládat do mezipaměti|Kořenový element.|Yes|

### <a name="attributes"></a>Atributy

| Název             | Popis                                                                                                                                                                                                                                                                                                                                                 | Povinné | Výchozí           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| doba trvání         | Doba až živých položek v mezipaměti, které jsou zadány v sekundách.     | Yes      | –               |
| mezipaměť – odpověď         | Nastavte na true, pokud chcete aktuální odpověď HTTP ukládat do mezipaměti. Pokud je atribut vynechán nebo je nastaven na hodnotu false, budou uloženy pouze odpovědi HTTP se stavovým kódem `200 OK` .                           | No      | false (nepravda)               |

### <a name="usage"></a>Využití
Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

- **Oddíly zásad:** odchozí
- **Obory zásad:** všechny rozsahy

## <a name="get-value-from-cache"></a><a name="GetFromCacheByKey"></a> Získat hodnotu z mezipaměti
Pomocí `cache-lookup-value` zásad můžete vyhledávat v mezipaměti pomocí klíče a vracet hodnotu uloženou v mezipaměti. Klíč může obsahovat libovolnou řetězcovou hodnotu a obvykle se poskytuje pomocí výrazu zásad.

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
Další informace a příklady těchto zásad najdete v tématu [vlastní ukládání do mezipaměti v Azure API Management](./api-management-sample-cache-by-key.md).

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Elementy

|Název|Popis|Povinné|
|----------|-----------------|--------------|
|mezipaměť – vyhledání hodnoty|Kořenový element.|Yes|

### <a name="attributes"></a>Atributy

| Název             | Popis                                                                                                                                                                                                                                                                                                                                                 | Povinné | Výchozí           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| typ ukládání do mezipaměti | Vyberte jednu z následujících hodnot atributu:<br />- `internal` Pokud chcete používat integrovanou API Management mezipaměť,<br />- `external` Pokud chcete použít externí mezipaměť, jak je popsáno v tématu [použití externí mezipaměti Azure pro Redis v Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` Pokud chcete použít externí mezipaměť v případě, že je nakonfigurovaná nebo interní mezipaměť, jinak. | No       | `prefer-external` |
| Výchozí hodnota    | Hodnota, která bude přiřazena proměnné v případě, že výsledkem hledání klíče mezipaměti bylo neúspěšné. Pokud tento atribut není zadán, `null` je přiřazen.                                                                                                                                                                                                           | No       | `null`            |
| key              | Hodnota klíče mezipaměti, která se má použít při vyhledávání                                                                                                                                                                                                                                                                                                                       | Yes      | –               |
| název proměnné    | Název [kontextové proměnné](api-management-policy-expressions.md#ContextVariables) , ke které je hledaná hodnota přiřazena, pokud vyhledávání proběhlo úspěšně. Pokud dojde k neúspěšnému vyhledávání, proměnné se přiřadí hodnota `default-value` atributu nebo `null` , pokud `default-value` je atribut vynechán.                                       | Yes      | –               |

### <a name="usage"></a>Využití
Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

- **Oddíly zásad:** příchozí, odchozí, back-end, zapnutá chyba
- **Obory zásad:** všechny rozsahy

## <a name="store-value-in-cache"></a><a name="StoreToCacheByKey"></a> Hodnota úložiště v mezipaměti
`cache-store-value`Služba provádí úložiště mezipaměti podle klíče. Klíč může obsahovat libovolnou řetězcovou hodnotu a obvykle se poskytuje pomocí výrazu zásad.

> [!NOTE]
> Operace uložení hodnoty v mezipaměti provedené touto zásadou je asynchronní. Uloženou hodnotu lze načíst pomocí zásad pro [získání hodnoty z mezipaměti](#GetFromCacheByKey) . Uložená hodnota však nemusí být okamžitě k dispozici pro načtení, protože asynchronní operace, která ukládá hodnotu v mezipaměti, může stále probíhat. 

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Příklad
Další informace a příklady těchto zásad najdete v tématu [vlastní ukládání do mezipaměti v Azure API Management](./api-management-sample-cache-by-key.md).

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Elementy

|Název|Popis|Povinné|
|----------|-----------------|--------------|
|cache-Store-Value|Kořenový element.|Yes|

### <a name="attributes"></a>Atributy

| Název             | Popis                                                                                                                                                                                                                                                                                                                                                 | Povinné | Výchozí           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| typ ukládání do mezipaměti | Vyberte jednu z následujících hodnot atributu:<br />- `internal` Pokud chcete používat integrovanou API Management mezipaměť,<br />- `external` Pokud chcete použít externí mezipaměť, jak je popsáno v tématu [použití externí mezipaměti Azure pro Redis v Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` Pokud chcete použít externí mezipaměť v případě, že je nakonfigurovaná nebo interní mezipaměť, jinak. | No       | `prefer-external` |
| doba trvání         | Hodnota se uloží do mezipaměti pro zadanou hodnotu trvání zadanou v sekundách.                                                                                                                                                                                                                                                                                 | Yes      | –               |
| key              | Klíč mezipaměti, do které se hodnota uloží.                                                                                                                                                                                                                                                                                                                   | Yes      | –               |
| hodnota            | Hodnota, která má být uložena do mezipaměti.                                                                                                                                                                                                                                                                                                                                     | Yes      | –               |
### <a name="usage"></a>Využití
Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

- **Oddíly zásad:** příchozí, odchozí, back-end, zapnutá chyba
- **Obory zásad:** všechny rozsahy

## <a name="remove-value-from-cache"></a><a name="RemoveCacheByKey"></a> Odebrat hodnotu z mezipaměti
`cache-remove-value`Odstraní položku uloženou v mezipaměti, kterou identifikuje její klíč. Klíč může obsahovat libovolnou řetězcovou hodnotu a obvykle se poskytuje pomocí výrazu zásad.

#### <a name="policy-statement"></a>Prohlášení o zásadách

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Příklad

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Elementy

|Název|Popis|Povinné|
|----------|-----------------|--------------|
|mezipaměť – Remove-Value|Kořenový element.|Yes|

#### <a name="attributes"></a>Atributy

| Název             | Popis                                                                                                                                                                                                                                                                                                                                                 | Povinné | Výchozí           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| typ ukládání do mezipaměti | Vyberte jednu z následujících hodnot atributu:<br />- `internal` Pokud chcete používat integrovanou API Management mezipaměť,<br />- `external` Pokud chcete použít externí mezipaměť, jak je popsáno v tématu [použití externí mezipaměti Azure pro Redis v Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` Pokud chcete použít externí mezipaměť v případě, že je nakonfigurovaná nebo interní mezipaměť, jinak. | No       | `prefer-external` |
| key              | Klíč hodnoty dříve uložené v mezipaměti, který má být odstraněn z mezipaměti.                                                                                                                                                                                                                                                                                        | Yes      | –               |

#### <a name="usage"></a>Využití
Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes) zásad.

- **Oddíly zásad:** příchozí, odchozí, back-end, zapnutá chyba
- **Obory zásad:** všechny rozsahy

## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami najdete v těchto tématech:

+ [Zásady v API Management](api-management-howto-policies.md)
+ [Transformační rozhraní API](transform-api.md)
+ [Odkaz na zásady](./api-management-policies.md) pro úplný seznam příkazů zásad a jejich nastavení
+ [Ukázky zásad](./policy-reference.md)