---
title: Azure API Management napříč zásady domény | Microsoft Docs
description: Další informace o k dispozici pro použití v Azure API Management napříč doménami zásady.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 590831454e8a18678e357b4824eb35a717d1fee0
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2017
ms.locfileid: "26129030"
---
# <a name="api-management-cross-domain-policies"></a>Zásady pro API Management napříč doménami
Toto téma obsahuje odkaz pro následující zásady služby API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady ve službě API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="CrossDomainPolicies"></a>Různé zásady domény  
  
-   [Povolit volání mezi doménami](api-management-cross-domain-policies.md#AllowCrossDomainCalls) -zpřístupní rozhraní API z Adobe Flash a Microsoft Silverlight založené na prohlížeči klientů.  
-   [CORS](api-management-cross-domain-policies.md#CORS) – přidává podporu (CORS), aby operace nebo rozhraní API umožňující volání napříč doménami založené na prohlížeči klientů pro sdílení prostředků různého původu.  
-   [JSONP](api-management-cross-domain-policies.md#JSONP) -přidá JSON s podporou odsazení (JSONP) operace nebo rozhraní API, chcete-li povolit volání mezi doménami z klientů JavaScript založené na prohlížeči.  
  
##  <a name="AllowCrossDomainCalls"></a>Povolit volání mezi doménami  
 Použití `cross-domain` zásady zpřístupněte rozhraní API z Adobe Flash a Microsoft Silverlight založené na prohlížeči klientů.  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<cross-domain>  
   <!-Policy configuration is in the Adobe cross-domain policy file format,   
      see http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->  
</cross-domain>  
```  
  
### <a name="example"></a>Příklad  
  
```xml  
<cross-domain>  
    <cross-domain-policy>  
        <allow-http-request-headers-from domain='*' headers='*' />  
    </cross-domain-policy>  
</cross-domain>  
```  
  
### <a name="elements"></a>Elementy  
  
|Name (Název)|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|mezi doménami|Kořenový element. Podřízené elementy musí odpovídat [specifikaci souboru zásady mezi doménami Adobe](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Ano|  
  
### <a name="usage"></a>Využití  
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásady:** příchozí  
-   **Zásady obory:** globální  
  
##  <a name="CORS"></a>CORS  
 `cors` Zásad přidá podporu (CORS), aby operace nebo rozhraní API umožňující volání napříč doménami založené na prohlížeči klientů pro sdílení prostředků různého původu.  
  
 CORS umožňuje prohlížečem a serverem komunikovat a zjistěte, zda chcete povolit konkrétní požadavky cross-origin (tj. XMLHttpRequests volání z jazyka JavaScript na webové stránce do jiných domén). To umožňuje více flexibility než povolíte jen žádostí stejné zdroji, ale je bezpečnější než povolení všech žádostí napříč zdroji.  
  
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
 Tento příklad ukazuje, jak pro podporu požadavků před letu, například těch, které se vlastní hlavičky nebo jiných metod než GET a POST. Chcete-li podporovat vlastní hlavičky a další příkazy HTTP, použijte `allowed-methods` a `allowed-headers` částech, jak je znázorněno v následujícím příkladu.  
  
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
|cors|Kořenový element.|Ano|Není k dispozici|  
|Povolené zdroje|Obsahuje `origin` prvky, které popisují povolené zdroje pro požadavky napříč doménami. `allowed-origins`může obsahovat buď jediný `origin` element, který určuje `*` umožňující jakýkoli původ nebo jeden nebo více `origin` elementy, které obsahují identifikátoru URI.|Ano|Není k dispozici|  
|Počátek|Hodnota může být buď `*` umožňuje všechny původy nebo identifikátor URI, který určuje jeden počátek. Identifikátor URI musí obsahovat schéma, hostitele a portu.|Ano|Při vynechání je port v identifikátoru URI je používá port 80 pro protokol HTTP a port 443 se používá pro protokol HTTPS.|  
|povolené metody|Tento prvek je nutný, pokud metody jiné než GET nebo POST jsou povoleny. Obsahuje `method` elementy, které určují, podporované příkazy HTTP.|Ne|Pokud v této části není zadán, jsou podporovány GET a POST.|  
|– Metoda|Určuje příkaz HTTP.|Alespoň jeden `method` prvek je nutný, pokud `allowed-methods` část je k dispozici.|Není k dispozici|  
|povolené hlavičky|Tento prvek obsahuje `header` elementy zadání názvy seznam hlaviček, které můžou být součástí požadavku.|Ne|Není k dispozici|  
|Zveřejněte hlavičky|Tento prvek obsahuje `header` elementy zadání názvy seznam hlaviček, které budou přístupné klientem.|Ne|Není k dispozici|  
|záhlaví|Určuje název hlavičky.|Alespoň jeden `header` element je vyžadována ve `allowed-headers` nebo `expose-headers` Pokud se nachází v části.|Není k dispozici|  
  
### <a name="attributes"></a>Atributy  
  
|Name (Název)|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|Povolit přihlašovací údaje|`Access-Control-Allow-Credentials` Hlavičky v předběžné odpovědi bude nastavena na hodnotu tohoto atributu a vliv na schopnost klienta odeslat přihlašovací údaje v žádosti napříč doménami.|Ne|False|  
|Kontrola před výstupem výsledek max-age|`Access-Control-Max-Age` Hlavičky v předběžné odpovědi bude nastavena na hodnotu tohoto atributu a vliv na schopnost uživatelský agent mezipaměti před letu odpovědi.|Ne|0|  
  
### <a name="usage"></a>Využití  
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásady:** příchozí  
-   **Zásady obory:** rozhraní API, operace  
  
##  <a name="JSONP"></a>JSONP  
 `jsonp` Zásad přidá JSON s podporou odsazení (JSONP) do operace nebo rozhraní API, chcete-li povolit volání mezi doménami z klientů JavaScript založené na prohlížeči. JSONP je metoda použitá v programech, JavaScript při žádosti o data ze serveru v jiné doméně. JSONP obchází omezení vynucená většina webových prohlížečů, kde přístup k webovým stránkám musí být ve stejné doméně.  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<jsonp callback-parameter-name="callback function name" />  
```  
  
### <a name="example"></a>Příklad  
  
```xml  
<jsonp callback-parameter-name="cb" />  
```  
  
 Pokud jste volali metodu bez parametru zpětného volání? cb = XXX vrátí prostý JSON (bez obálku volání funkce).  
  
 Pokud přidáte parametr zpětného volání `?cb=XXX` vrátí výsledek JSONP, zabalení původní JSON jako výsledky kolem funkce zpětného volání`XYZ('<json result goes here>');`  
  
### <a name="elements"></a>Elementy  
  
|Name (Název)|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|JSONP|Kořenový element.|Ano|  
  
### <a name="attributes"></a>Atributy  
  
|Name (Název)|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|Název parametru zpětného volání|Volání pro funkce JavaScript se mezi doménami, předponu název plně kvalifikované domény, kde se funkce nachází.|Ano|Není k dispozici|  
  
### <a name="usage"></a>Využití  
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásady:** odchozí  
-   **Zásady obory:** globální, produktu, rozhraní API, operace  
  
## <a name="next-steps"></a>Další kroky

Práce se zásadami pro další informace najdete v tématu:

+ [Zásady ve službě API Management](api-management-howto-policies.md)
+ [Transformuje rozhraní API](transform-api.md)
+ [Referenční informace o zásadách](api-management-policy-reference.md) pro úplný seznam příkazy zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)   