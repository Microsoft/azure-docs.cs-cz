---
title: Zásady správy azure api napříč doménami | Dokumenty společnosti Microsoft
description: Přečtěte si o zásadách pro více domén, které jsou k dispozici pro použití ve správě rozhraní Azure API.
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
ms.openlocfilehash: b72abf4e208c57987375a105865046f194460058
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265984"
---
# <a name="api-management-cross-domain-policies"></a>Zásady pro API Management napříč doménami
Toto téma obsahuje odkaz na následující zásady správy rozhraní API. Informace o přidávání a konfiguraci zásad naleznete [v tématu Zásady ve správě rozhraní API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="cross-domain-policies"></a><a name="CrossDomainPolicies"></a>Zásady mezi doménami

- [Povolit volání mezi doménami](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – zpřístupní rozhraní API z aplikací Adobe Flash a klientů založených na prohlížeči Microsoft Silverlight.
- [CORS](api-management-cross-domain-policies.md#CORS) – přidá podporu sdílení prostředků mezi zdroji (CORS) do operace nebo rozhraní API pro povolení volání mezi doménami z klientů založených na prohlížeči.
- [JSONP](api-management-cross-domain-policies.md#JSONP) - Přidá JSON s podporou odsazení (JSONP) pro operaci nebo rozhraní API pro povolení volání mezi doménami z klientů založených na prohlížeči JavaScript.

## <a name="allow-cross-domain-calls"></a><a name="AllowCrossDomainCalls"></a>Povolit volání mezi doménami
Pomocí `cross-domain` této zásady můžete rozhraní API zpřístupnit klientům adobe flash a microsoft silverlight založeným na prohlížečích.

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

|Name (Název)|Popis|Požaduje se|
|----------|-----------------|--------------|
|mezi doménami|Kořenový prvek. Podřízené prvky musí odpovídat [specifikaci souboru zásad adobe mezi doménami](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Ano|

### <a name="usage"></a>Využití
Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Oddíly zásad:** příchozí
- **Obory zásad:** všechny obory

## <a name="cors"></a><a name="CORS"></a>CORS
Zásada `cors` přidává podporu sdílení prostředků mezi zdroji (CORS) pro operaci nebo rozhraní API pro povolení volání mezi doménami z klientů založených na prohlížeči.

CORS umožňuje prohlížeči a serveru komunikovat a určit, zda mají být povoleny konkrétní požadavky na příčný původ (tj. volání XMLHttpRequests z JavaScriptu na webové stránce do jiných domén). To umožňuje větší flexibilitu než pouze povolení požadavků stejného původu, ale je bezpečnější než povolení všech požadavků na příčný původ.

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
Tento příklad ukazuje, jak podporovat požadavky před letem, například ty s vlastní záhlaví nebo metody než GET a POST. Chcete-li podporovat vlastní záhlaví a další `allowed-methods` `allowed-headers` slovesa HTTP, použijte oddíly a, jak je znázorněno v následujícím příkladu.

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

|Name (Název)|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|cors|Kořenový prvek.|Ano|Není dostupné.|
|povolené původy|Obsahuje `origin` prvky, které popisují povolené původy pro požadavky mezi doménami. `allowed-origins`může obsahovat `origin` jeden prvek, `*` který určuje povolit libovolný `origin` původ, nebo jeden nebo více prvků, které obsahují identifikátor URI.|Ano|Není dostupné.|
|origin (zdroj)|Hodnota může být `*` buď povolit všechny počátky nebo IDENTIFIKÁTOR URI, který určuje jeden původ. Identifikátor URI musí obsahovat schéma, hostitele a port.|Ano|Pokud je port vynechán v identifikátoru URI, port 80 se používá pro protokol HTTP a port 443 se používá pro protokol HTTPS.|
|povolené metody|Tento prvek je vyžadován, pokud jsou povoleny jiné metody než GET nebo POST. Obsahuje `method` prvky, které určují podporované http slovesa. Hodnota `*` označuje všechny metody.|Ne|Pokud tato část není k dispozici, GET a POST jsou podporovány.|
|method|Určuje sloveso HTTP.|Alespoň jeden `method` prvek je `allowed-methods` vyžadován, pokud je oddíl přítomen.|Není dostupné.|
|povolené hlavičky|Tento prvek `header` obsahuje prvky určující názvy záhlaví, které mohou být zahrnuty v požadavku.|Ne|Není dostupné.|
|vystavit záhlaví|Tento prvek `header` obsahuje prvky určující názvy záhlaví, které budou přístupné klientem.|Ne|Není dostupné.|
|záhlaví|Určuje název záhlaví.|Alespoň jeden `header` prvek je `allowed-headers` `expose-headers` požadováno v nebo pokud je oddíl přítomen.|Není dostupné.|

### <a name="attributes"></a>Atributy

|Name (Název)|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|pověření povolených přihlašovacích údajů|Záhlaví `Access-Control-Allow-Credentials` odpovědi kontroly před výstupem bude nastaveno na hodnotu tohoto atributu a ovlivní schopnost klienta odesílat pověření v požadavcích na více domén.|Ne|false (nepravda)|
|výstupek-výsledek-max-věk|Záhlaví `Access-Control-Max-Age` v odpovědi kontroly před výstupem bude nastaveno na hodnotu tohoto atributu a ovlivní schopnost uživatelského agenta ukládat odpověď před odletem do mezipaměti.|Ne|0|

### <a name="usage"></a>Využití
Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Oddíly zásad:** příchozí
- **Obory zásad:** všechny obory

## <a name="jsonp"></a><a name="JSONP"></a>Jsonp
Zásada `jsonp` přidává JSON s podporou odsazení (JSONP) do operace nebo rozhraní API pro povolení volání mezi doménami z klientů založených na prohlížeči JavaScript. JSONP je metoda používaná v javascriptových programech k vyžádání dat ze serveru v jiné doméně. JSONP obchází omezení vynucené většinou webových prohlížečů, kde přístup k webovým stránkám musí být ve stejné doméně.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Příklad

```xml
<jsonp callback-parameter-name="cb" />
```

Pokud zavoláte metodu bez parametru zpětného volání ?cb=XXX vrátí prostý JSON (bez obálky volání funkce).

Pokud přidáte parametr `?cb=XXX` zpětného volání, vrátí výsledek JSONP a obtéká původní výsledky JSON kolem funkce zpětného volání, jako je`XYZ('<json result goes here>');`

### <a name="elements"></a>Elementy

|Name (Název)|Popis|Požaduje se|
|----------|-----------------|--------------|
|Jsonp|Kořenový prvek.|Ano|

### <a name="attributes"></a>Atributy

|Name (Název)|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|název parametru zpětného volání|Volání funkce JavaScript mezi doménami s předponou s plně kvalifikovaným názvem domény, ve kterém je funkce umístěna.|Ano|Není dostupné.|

### <a name="usage"></a>Využití
Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Oddíly zásad:** odchozí
- **Obory zásad:** všechny obory

## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami naleznete v tématu:

+ [Zásady ve správě rozhraní API](api-management-howto-policies.md)
+ [Transformovat api](transform-api.md)
+ [Odkaz na zásady](api-management-policy-reference.md) pro úplný seznam prohlášení zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)
