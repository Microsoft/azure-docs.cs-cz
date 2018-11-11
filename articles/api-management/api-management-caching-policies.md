---
title: Zásady ukládání do mezipaměti Azure API Management | Dokumentace Microsoftu
description: Další informace o ukládání do mezipaměti zásad, které jsou k dispozici pro použití ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 1706364ca0281240b5b887bea219620c7b4add5e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246833"
---
# <a name="api-management-caching-policies"></a>Zásady ukládání do mezipaměti služby API Management
Toto téma obsahuje odkaz pro následující zásady služby API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady ve službě API Management](https://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="CachingPolicies"></a> Zásady ukládání do mezipaměti  
  
-   Zásady ukládání do mezipaměti odpovědi  
  
    -   [Získat z mezipaměti](api-management-caching-policies.md#GetFromCache) -provádět mezipaměti vyhledání a vrácení platné v mezipaměti odpovědi, pokud je k dispozici.  
    -   [Store do mezipaměti](api-management-caching-policies.md#StoreToCache) -ukládá do mezipaměti odpovědi závislosti na konfiguraci mezipaměti zadaný ovládací prvek.  
  
-   Hodnota zásad ukládání do mezipaměti  

    -   [Získat hodnotu z mezipaměti](#GetFromCacheByKey) – načtení položky v mezipaměti podle klíče. 
    -   [Store hodnota v mezipaměti](#StoreToCacheByKey) -Store položku v mezipaměti podle klíče. 
    -   [Odebrat hodnotu z mezipaměti](#RemoveCacheByKey) -odebrat položku v mezipaměti podle klíče.  
  
##  <a name="GetFromCache"></a> Získat z mezipaměti  
 Použití `cache-lookup` zásad mezipaměti provést vyhledávání a vracet platnou odpověď uložená v mezipaměti-li k dispozici. Tuto zásadu lze použít v případech, kde zůstává statický obsah odpovědi po určitou dobu. Ukládání odpovědí do mezipaměti snižuje šířku pásma a požadavky na zpracování uložených na back-endu webového serveru a snižuje latenci vnímají spotřebitelé rozhraní API.  
  
> [!NOTE]
>  Tato zásada musí mít odpovídající [Store do mezipaměti](api-management-caching-policies.md#StoreToCache) zásad.  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">  
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
  
#### <a name="example"></a>Příklad:  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true">  
            <vary-by-query-parameter>version</vary-by-query-parameter>  
        </cache-lookup>           
    </inbound>  
    <outbound>  
        <cache-store duration="seconds" />  
        <base />          
    </outbound>  
</policies>  
```  
  
#### <a name="example-using-policy-expressions"></a>Příklad použití výrazy zásad  
 Tento příklad ukazuje, jak nakonfigurovat správu rozhraní API ukládání do mezipaměti Doba trvání odpovědi, která odpovídá ukládání odpovědí do mezipaměti této služby back-end jako zadané zálohované služby `Cache-Control` směrnice. Ukázka konfigurace a používání této zásady, najdete v části [Cloud Cover epizodě 177: Další funkce API Management s Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) a přechod na libovolný krok k 25:25.  
  
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
  
 Další informace najdete v tématu [výrazy zásad](api-management-policy-expressions.md) a [kontextovou proměnnou](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="elements"></a>Elementy  
  
|Název|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|vyhledávání v mezipaměti|Kořenový element.|Ano|  
|měnit podle hlavičky|Spustit ukládání do mezipaměti odpovědi na hodnotu zadanou hlavičku, třeba přijmout, Accept-Charset, Accept-Encoding, Accept-Language autorizaci Expect, z hostitele, If-Match.|Ne|  
|liší podle--parametru dotazu|Spuštění, ukládání do mezipaměti odpovědi na hodnotu zadaným parametrům dotazu. Zadejte jednu nebo více parametrů. Použijte jako oddělovač středník. Pokud nejsou zadány žádné, použijí se všechny parametry dotazu.|Ne|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|Povolit privátní – odpověď-ukládání do mezipaměti|Pokud je nastavena na `true`, umožňuje ukládání do mezipaměti požadavky, které obsahují také hlavičku ověřování.|Ne|false (nepravda)|  
|podřízený typ ukládání do mezipaměti|Tento atribut musí být nastaven na jednu z následujících hodnot.<br /><br /> -žádný - podřízené ukládání do mezipaměti není povolený.<br />-soukromé - příjem dat soukromých ukládání do mezipaměti je povolen.<br />-public - privátní a sdílené podřízené ukládání do mezipaměti je povolen.|Ne|žádný|  
|musí revalidate|Pokud je povoleno ukládání do mezipaměti podřízené tento atribut Zapne nebo vypne `must-revalidate` – direktiva ovládacího prvku mezipaměti v odpovědi brány.|Ne|true (pravda)|  
|se liší podle developer|Nastavte na `true` do mezipaměti odpovědi na klíč pro vývojáře.|Ano||  
|se liší podle – pro vývojáře – skupiny|Nastavte na `true` do mezipaměti odpovědi na roli uživatele.|Ano||  
  
### <a name="usage"></a>Využití  
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásad:** příchozí  
-   **Zásady obory:** rozhraní API, operace, produkt  
  
##  <a name="StoreToCache"></a> Store do mezipaměti  
 `cache-store` Zásady ukládání do mezipaměti odpovědi podle nastavení zadané mezipaměti. Tuto zásadu lze použít v případech, kde zůstává statický obsah odpovědi po určitou dobu. Ukládání odpovědí do mezipaměti snižuje šířku pásma a požadavky na zpracování uložených na back-endu webového serveru a snižuje latenci vnímají spotřebitelé rozhraní API.  
  
> [!NOTE]
>  Tato zásada musí mít odpovídající [získat z mezipaměti](api-management-caching-policies.md#GetFromCache) zásad.  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<cache-store duration="seconds" />  
```  
  
### <a name="examples"></a>Příklady  
  
#### <a name="example"></a>Příklad:  
  
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
  
#### <a name="example-using-policy-expressions"></a>Příklad použití výrazy zásad  
 Tento příklad ukazuje, jak nakonfigurovat správu rozhraní API ukládání do mezipaměti Doba trvání odpovědi, která odpovídá ukládání odpovědí do mezipaměti této služby back-end jako zadané zálohované služby `Cache-Control` směrnice. Ukázka konfigurace a používání této zásady, najdete v části [Cloud Cover epizodě 177: Další funkce API Management s Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) a přechod na libovolný krok k 25:25.  
  
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
  
 Další informace najdete v tématu [výrazy zásad](api-management-policy-expressions.md) a [kontextovou proměnnou](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="elements"></a>Elementy  
  
|Název|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|úložiště mezipaměti|Kořenový element.|Ano|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|doba trvání|Time-to-live uložené v mezipaměti položek, které se zadávají v sekundách.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásad:** odchozí    
-   **Zásady obory:** rozhraní API, operace, produkt  
  
##  <a name="GetFromCacheByKey"></a> Získat hodnotu z mezipaměti  
 Použití `cache-lookup-value` zásad provádět vyhledávání v mezipaměti podle klíče a vrátí hodnotu uloženou v mezipaměti. Klíč může obsahovat libovolné řetězcovou hodnotu a se většinou poskytuje pomocí výrazů zásad.  
  
> [!NOTE]
>  Tato zásada musí mít odpovídající [Store hodnota v mezipaměti](#StoreToCacheByKey) zásad.  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<cache-lookup-value key="cache key value"   
    default-value="value to use if cache lookup resulted in a miss"   
    variable-name="name of a variable looked up value is assigned to" />  
```  
  
### <a name="example"></a>Příklad:  
 Další informace a příklady těchto zásad najdete v tématu [vlastní ukládání do mezipaměti ve službě Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).  
  
```xml  
<cache-lookup-value  
    key="@("userprofile-" + context.Variables["enduserid"])"    
    variable-name="userprofile" />  
  
```  
  
### <a name="elements"></a>Elementy  
  
|Název|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|hodnota mezipaměti vyhledávání|Kořenový element.|Ano|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|Výchozí hodnota|Hodnota, která se přiřadí proměnné if vyhledávání klíčů mezipaměti výsledkem by chyběla. Pokud tento atribut není zadán, `null` je přiřazen.|Ne|`null`|  
|key|Hodnota klíče mezipaměti používané k vyhledávání.|Ano|neuvedeno|  
|Název proměnné|Název [kontextovou proměnnou](api-management-policy-expressions.md#ContextVariables) looked nahoru hodnota se přiřadí, pokud je úspěšné vyhledávání. Pokud je vyhledávání výsledkem by chyběla, proměnné se přiřadí hodnotu `default-value` atribut nebo `null`, pokud `default-value` atribut je vynechán.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásad:** vstupní, výstupní a back-endu na chyby  
-   **Zásady obory:** globální, rozhraní API, operace, produkt  
  
##  <a name="StoreToCacheByKey"></a> Hodnota Store v mezipaměti  
 `cache-store-value` Provádí úložiště mezipaměti podle klíče. Klíč může obsahovat libovolné řetězcovou hodnotu a se většinou poskytuje pomocí výrazů zásad.  
  
> [!NOTE]
>  Tato zásada musí mít odpovídající [získat hodnotu z mezipaměti](#GetFromCacheByKey) zásad.  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<cache-store-value key="cache key value" value="value to cache" duration="seconds" />  
```  
  
### <a name="example"></a>Příklad:  
 Další informace a příklady těchto zásad najdete v tématu [vlastní ukládání do mezipaměti ve službě Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).  
  
```xml  
<cache-store-value  
    key="@("userprofile-" + context.Variables["enduserid"])"  
    value="@((string)context.Variables["userprofile"])" duration="100000" />  
  
```  
  
### <a name="elements"></a>Elementy  
  
|Název|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|Hodnota úložiště mezipaměti|Kořenový element.|Ano|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|doba trvání|Hodnota bude do mezipaměti pro hodnotu zadaná doba trvání zadávají v sekundách.|Ano|neuvedeno|  
|key|V části se má uložit klíč mezipaměti hodnotu.|Ano|neuvedeno|  
|hodnota|Hodnota ukládat do mezipaměti.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásad:** vstupní, výstupní a back-endu na chyby  
-   **Zásady obory:** globální, rozhraní API, operace, produkt  
  
###  <a name="RemoveCacheByKey"></a> Odebrat hodnotu z mezipaměti  
`cache-remove-value` Odstraní položku v mezipaměti, identifikovat podle jeho klíče. Klíč může obsahovat libovolné řetězcovou hodnotu a se většinou poskytuje pomocí výrazů zásad.  
  
#### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
  
<cache-remove-value key="cache key value"/>  
  
```  
  
#### <a name="example"></a>Příklad:  
  
```xml  
  
<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>  
  
```  
  
#### <a name="elements"></a>Elementy  
  
|Název|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|hodnota mezipaměti odebrat|Kořenový element.|Ano|  
  
#### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|key|Klíče uložené v mezipaměti hodnota, která má být odebrány z mezipaměti.|Ano|neuvedeno|  
  
#### <a name="usage"></a>Využití  
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .  
  
-   **Části zásad:** vstupní, výstupní a back-endu na chyby  
-   **Zásady obory:** globální, rozhraní API, operace, produkt  

## <a name="next-steps"></a>Další postup

Práce se zásadami pro další informace najdete v tématu:

+ [Zásady ve službě API Management](api-management-howto-policies.md)
+ [Transformujte rozhraní API](transform-api.md)
+ [Referenční příručce o zásadách](api-management-policy-reference.md) úplný seznam zásad příkazy a jejich nastavení
+ [Ukázky zásad](policy-samples.md)   
