---
title: Zásady pro Azure API Management mezi doménami | Microsoft Docs
description: Přečtěte si o zásadách pro více doménách, které jsou dostupné pro použití v Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: db64a2f64c592a62f621355047a7bc9844d66457
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375068"
---
# <a name="api-management-cross-domain-policies"></a>Zásady pro API Management napříč doménami
V tomto tématu najdete referenční informace pro následující zásady API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady v API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CrossDomainPolicies"></a>Zásady pro více domén

- [Povolení mezidoménových volání](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – ZPŘÍSTUPŇUJE rozhraní API z klientů Adobe Flash a prohlížeče založeného na prohlížeči Microsoft Silverlight.
- [CORS](api-management-cross-domain-policies.md#CORS) – přidává podporu pro sdílení prostředků mezi zdroji (CORS) pro operaci nebo rozhraní API, která umožňuje mezidoménová volání z klientů založených na prohlížeči.
- [JSONP](api-management-cross-domain-policies.md#JSONP) – přidá do operace nebo do rozhraní API podporu formátu JSON s doplňováním (JSONP), která umožňuje mezidoménová volání z klientů v prohlížeči JavaScript.

## <a name="AllowCrossDomainCalls"></a>Povolení volání mezi doménami
Pomocí zásad `cross-domain` zpřístupněte rozhraní API z klientů založených na prohlížeči Adobe Flash a Microsoft Silverlight.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<cross-domain>
    <!-Policy configuration is in the Adobe cross-domain policy file format,
        see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->
</cross-domain>
```

### <a name="example"></a>Příklad

```xml
<cross-domain>
    <cross-domain>
        <allow-http-request-headers-from domain='*' headers='*' />
    </cross-domain>
</cross-domain>
```

### <a name="elements"></a>Elementy

|Název|Popis|Požaduje se|
|----------|-----------------|--------------|
|mezi doménami|Kořenový element. Podřízené elementy musí odpovídat [specifikaci souboru zásad pro Adobe více domén](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Ano|

### <a name="usage"></a>Využití
Tyto zásady se dají použít v následujících [oddílech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zásad.

- **Oddíly zásad:** příchozí
- **Obory zásad:** všechny rozsahy

## <a name="CORS"></a>CORS
Zásada `cors` přidává podporu pro sdílení prostředků mezi zdroji (CORS) pro operaci nebo rozhraní API, která umožňuje mezidoménová volání z klientů založených na prohlížeči.

CORS umožňuje prohlížeči a serveru spolupracovat a určit, jestli se mají povolit konkrétní požadavky na více zdrojů (tj. volání XMLHttpRequest prováděné z JavaScriptu na webové stránce v jiných doménách). Díky tomu je větší flexibilita než povolování pouze stejných požadavků, ale je bezpečnější než povolení všech žádostí mezi zdroji.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<cors allow-credentials="false|true">
    <allowed-origins>
        <origin>origin uri</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="number of seconds">
        <method>http verb</method>
    </allowed-methods>
    <allowed-headers>
        <header>header name</header>
    </allowed-headers>
    <expose-headers>
        <header>header name</header>
    </expose-headers>
</cors>
```

### <a name="example"></a>Příklad
Tento příklad ukazuje, jak podporovat žádosti o předběžné lety, jako jsou ty s vlastními záhlavími nebo metodami, které jsou jiné než GET a POST. Pro podporu vlastních hlaviček a dalších příkazů HTTP použijte `allowed-methods` a `allowed-headers` oddíly, jak je znázorněno v následujícím příkladu.

```xml
<cors allow-credentials="true">
    <allowed-origins>
        <!-- Localhost useful for development -->
        <origin>http://localhost:8080/</origin>
        <origin>http://example.com/</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="300">
        <method>GET</method>
        <method>POST</method>
        <method>PATCH</method>
        <method>DELETE</method>
    </allowed-methods>
    <allowed-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
        <header>x-zumo-version</header>
        <header>x-zumo-auth</header>
        <header>content-type</header>
        <header>accept</header>
    </allowed-headers>
    <expose-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
    </expose-headers>
</cors>
```

### <a name="elements"></a>Elementy

|Název|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|CORS|Kořenový element.|Ano|neuvedeno|
|povolené – počátek|Obsahuje `origin` prvky, které popisují povolené zdroje pro požadavky mezi doménami. `allowed-origins` může obsahovat jeden prvek `origin`, který určuje `*` pro povolení jakéhokoli původu, nebo jeden nebo více `origin` prvků, které obsahují identifikátor URI.|Ano|neuvedeno|
|počátek|Hodnota může být buď `*`, aby povolovala všechny zdroje, nebo identifikátor URI, který určuje jeden počátek. Identifikátor URI musí obsahovat schéma, hostitele a port.|Ano|Pokud je port v identifikátoru URI vynechán, použije se port 80 pro protokol HTTP a port 443 pro protokol HTTPS.|
|povolené – metody|Tento prvek je vyžadován, pokud jsou povoleny jiné metody než GET nebo POST. Obsahuje prvky `method`, které určují podporované příkazy HTTP.|Ne|Pokud tato část není k dispozici, jsou podporovány GET a POST.|
|method|Určuje příkaz HTTP.|Pokud je k dispozici oddíl `allowed-methods`, je vyžadován alespoň jeden prvek `method`.|neuvedeno|
|povoleno – hlavičky|Tento prvek obsahuje `header` prvky určující názvy hlaviček, které mohou být zahrnuty v žádosti.|Ne|neuvedeno|
|vystavení – hlavičky|Tento prvek obsahuje `header` prvky určující názvy hlaviček, které budou přístupné pro klienta.|Ne|neuvedeno|
|záhlaví|Určuje název záhlaví.|V `allowed-headers` nebo `expose-headers` je vyžadován alespoň jeden prvek `header`, pokud je oddíl přítomen.|neuvedeno|

### <a name="attributes"></a>Atributy

|Název|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|allow-credentials|Záhlaví `Access-Control-Allow-Credentials` v odpovědi na kontrolu před výstupem bude nastaveno na hodnotu tohoto atributu a bude mít vliv na schopnost klienta odesílat přihlašovací údaje v rámci požadavků mezi doménami.|Ne|false (nepravda)|
|Kontrola před výstupem – výsledek – maximum – stáří|Záhlaví `Access-Control-Max-Age` v odpovědi na kontrolu před výstupem bude nastaveno na hodnotu tohoto atributu a bude mít vliv na schopnost uživatelského agenta ukládat do mezipaměti před letem.|Ne|0|

### <a name="usage"></a>Využití
Tyto zásady se dají použít v následujících [oddílech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zásad.

- **Oddíly zásad:** příchozí
- **Obory zásad:** všechny rozsahy

## <a name="JSONP"></a>JSONP
Zásada `jsonp` přidá JSON s podporou odsazení formátu (JSONP) k operaci nebo rozhraní API, aby bylo možné v klientech založených na prohlížeči JavaScript umožňovat mezidoménová volání. JSONP je metoda, která se používá v programech JavaScript k vyžádání dat ze serveru v jiné doméně. JSONP obchází omezení vyžadované většinou webových prohlížečů, kde musí být přístup k webovým stránkám ve stejné doméně.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Příklad

```xml
<jsonp callback-parameter-name="cb" />
```

Pokud zavoláte metodu bez parametru zpětného volání? ' c = XXX vrátí se do obyčejného formátu JSON (bez obálky volání funkce).

Pokud přidáte parametr zpětného volání `?cb=XXX` vrátí výsledek JSONP a zabalí původní výsledky JSON kolem funkce zpětného volání, jako je `XYZ('<json result goes here>');`

### <a name="elements"></a>Elementy

|Název|Popis|Požaduje se|
|----------|-----------------|--------------|
|jsonp|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Název|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|zpětné volání – parametr-Name|Funkce jazyka JavaScript mezi doménami začíná předponou s plně kvalifikovaným názvem domény, kde se nachází funkce.|Ano|neuvedeno|

### <a name="usage"></a>Využití
Tyto zásady se dají použít v následujících [oddílech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zásad.

- **Oddíly zásad:** odchozí
- **Obory zásad:** všechny rozsahy

## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami najdete v těchto tématech:

+ [Zásady v API Management](api-management-howto-policies.md)
+ [Transformační rozhraní API](transform-api.md)
+ [Odkaz na zásady](api-management-policy-reference.md) pro úplný seznam příkazů zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)
