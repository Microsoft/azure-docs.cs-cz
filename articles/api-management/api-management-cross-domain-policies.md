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
ms.date: 07/14/2020
ms.author: apimpm
ms.openlocfilehash: 31d26769fa3ef49684f8a2eedf6a0691316e742b
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071230"
---
# <a name="api-management-cross-domain-policies"></a>Zásady pro API Management napříč doménami
V tomto tématu najdete referenční informace pro následující zásady API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady v API Management](./api-management-policies.md).

## <a name="cross-domain-policies"></a><a name="CrossDomainPolicies"></a> Zásady pro více domén

- [Povolení mezidoménových volání](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – ZPŘÍSTUPŇUJE rozhraní API z klientů Adobe Flash a prohlížeče založeného na prohlížeči Microsoft Silverlight.
- [CORS](api-management-cross-domain-policies.md#CORS) – přidává podporu pro sdílení prostředků mezi zdroji (CORS) pro operaci nebo rozhraní API, která umožňuje mezidoménová volání z klientů založených na prohlížeči.
- [JSONP](api-management-cross-domain-policies.md#JSONP) – přidá do operace nebo do rozhraní API podporu formátu JSON s doplňováním (JSONP), která umožňuje mezidoménová volání z klientů v prohlížeči JavaScript.

## <a name="allow-cross-domain-calls"></a><a name="AllowCrossDomainCalls"></a> Povolení volání mezi doménami
Použijte `cross-domain` zásady, aby rozhraní API bylo dostupné z klientů založených na prohlížeči Adobe Flash a Microsoft Silverlight.

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

|Název|Popis|Povinné|
|----------|-----------------|--------------|
|mezi doménami|Kořenový element. Podřízené elementy musí odpovídat [specifikaci souboru zásad pro Adobe více domén](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Yes|

### <a name="usage"></a>Využití
Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

- **Oddíly zásad:** příchozí
- **Obory zásad:** všechny rozsahy

## <a name="cors"></a><a name="CORS"></a> CORS
Tato `cors` zásada přidá podporu pro sdílení prostředků mezi zdroji (CORS) pro operaci nebo rozhraní API, která umožňuje mezidoménová volání z klientů založených na prohlížeči.

CORS umožňuje prohlížeči a serveru spolupracovat a určit, jestli se mají povolit konkrétní požadavky na více zdrojů (tj. volání XMLHttpRequest prováděné z JavaScriptu na webové stránce v jiných doménách). Díky tomu je větší flexibilita než povolování pouze stejných požadavků, ale je bezpečnější než povolení všech žádostí mezi zdroji.

Pokud chcete povolit interaktivní konzolu na portálu pro vývojáře, musíte použít zásadu CORS. Podrobnosti najdete v [dokumentaci k portálu pro vývojáře](./api-management-howto-developer-portal.md#cors) .

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
Tento příklad ukazuje, jak podporovat žádosti o předběžné lety, jako jsou ty s vlastními záhlavími nebo metodami, které jsou jiné než GET a POST. Pro podporu vlastních hlaviček a dalších příkazů HTTP použijte `allowed-methods` oddíly a, `allowed-headers` jak je znázorněno v následujícím příkladu.

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

|Název|Popis|Povinné|Výchozí|
|----------|-----------------|--------------|-------------|
|CORS|Kořenový element.|Yes|Není k dispozici|
|povolené – počátek|Obsahuje `origin` prvky, které popisují povolené zdroje pro požadavky mezi doménami. `allowed-origins` může obsahovat jeden `origin` prvek, který určuje, že `*` má být povolen libovolný původ, nebo jeden či více `origin` prvků, které obsahují identifikátor URI.|Yes|Není k dispozici|
|origin (zdroj)|Hodnota může být buď `*` tak, aby povolovala všechny zdroje, nebo identifikátor URI, který určuje jeden počátek. Identifikátor URI musí obsahovat schéma, hostitele a port.|Yes|Pokud je port v identifikátoru URI vynechán, použije se port 80 pro protokol HTTP a port 443 pro protokol HTTPS.|
|povolené – metody|Tento prvek je vyžadován, pokud jsou povoleny jiné metody než GET nebo POST. Obsahuje `method` prvky, které určují podporované příkazy HTTP. Hodnota `*` označuje všechny metody.|No|Pokud tato část není k dispozici, jsou podporovány GET a POST.|
|method|Určuje příkaz HTTP.|`method`Pokud je oddíl přítomen, je vyžadován alespoň jeden prvek `allowed-methods` .|Není k dispozici|
|povoleno – hlavičky|Tento prvek obsahuje `header` prvky, které určují názvy hlaviček, které mohou být zahrnuty v žádosti.|No|Není k dispozici|
|vystavení – hlavičky|Tento prvek obsahuje `header` prvky, které určují názvy hlaviček, které budou přístupné pro klienta.|No|Není k dispozici|
|header|Určuje název záhlaví.|`header`V nebo je- `allowed-headers` li oddíl přítomen, je požadován alespoň jeden prvek `expose-headers` .|Není k dispozici|

### <a name="attributes"></a>Atributy

|Název|Popis|Povinné|Výchozí|
|----------|-----------------|--------------|-------------|
|povolení – pověření|`Access-Control-Allow-Credentials`Záhlaví v odpovědi na kontrolu před výstupem bude nastaveno na hodnotu tohoto atributu a bude mít vliv na schopnost klienta odesílat přihlašovací údaje v rámci požadavků mezi doménami.|No|false (nepravda)|
|Kontrola před výstupem – výsledek – maximum – stáří|`Access-Control-Max-Age`Záhlaví v odpovědi na kontrolu před výstupem bude nastaveno na hodnotu tohoto atributu a bude mít vliv na schopnost uživatelského agenta ukládat do mezipaměti před letem.|No|0|

### <a name="usage"></a>Využití
Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

- **Oddíly zásad:** příchozí
- **Obory zásad:** všechny rozsahy

## <a name="jsonp"></a><a name="JSONP"></a> JSONP
`jsonp`Zásada přidá JSON s podporou odsazení formátu (JSONP) k operaci nebo rozhraní API, aby bylo možné v klientech založených na prohlížeči JavaScript umožňovat mezidoménová volání. JSONP je metoda, která se používá v programech JavaScript k vyžádání dat ze serveru v jiné doméně. JSONP obchází omezení vyžadované většinou webových prohlížečů, kde musí být přístup k webovým stránkám ve stejné doméně.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Příklad

```xml
<jsonp callback-parameter-name="cb" />
```

Pokud zavoláte metodu bez parametru zpětného volání? ' c = XXX vrátí se do obyčejného formátu JSON (bez obálky volání funkce).

Pokud přidáte parametr zpětného volání, `?cb=XXX` vrátí výsledek JSONP a zabalí původní výsledky JSON kolem funkce zpětného volání, jako např. `XYZ('<json result goes here>');`

### <a name="elements"></a>Elementy

|Název|Popis|Povinné|
|----------|-----------------|--------------|
|JSONP|Kořenový element.|Yes|

### <a name="attributes"></a>Atributy

|Název|Popis|Povinné|Výchozí|
|----------|-----------------|--------------|-------------|
|zpětné volání – parametr-Name|Funkce jazyka JavaScript mezi doménami začíná předponou s plně kvalifikovaným názvem domény, kde se nachází funkce.|Yes|Není k dispozici|

### <a name="usage"></a>Využití
Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

- **Oddíly zásad:** odchozí
- **Obory zásad:** všechny rozsahy

## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami najdete v těchto tématech:

+ [Zásady v API Management](api-management-howto-policies.md)
+ [Transformační rozhraní API](transform-api.md)
+ [Odkaz na zásady](./api-management-policies.md) pro úplný seznam příkazů zásad a jejich nastavení
+ [Ukázky zásad](./policy-reference.md)