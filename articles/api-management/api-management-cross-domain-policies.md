---
title: Mezidoménové zásady Azure API Management | Dokumentace Microsoftu
description: Další informace týkající se dají používat ve službě Azure API Management napříč doménami zásad.
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
ms.openlocfilehash: bd5082d9f66e65bb487c9216131b7e13f825340e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246646"
---
# <a name="api-management-cross-domain-policies"></a>Zásady pro API Management napříč doménami
Toto téma obsahuje odkaz pro následující zásady služby API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady ve službě API Management](https://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="CrossDomainPolicies"></a> Mezidoménové zásady  
  
-   [Povolit volání mezi doménami](api-management-cross-domain-policies.md#AllowCrossDomainCalls) -zpřístupňuje rozhraní API z Adobe Flash a Silverlight Microsoftu založené na prohlížeči klientů.  
-   [CORS](api-management-cross-domain-policies.md#CORS) – přidá prostředků mezi zdroji (CORS) podporu o operaci nebo rozhraní API pro povolit volání mezi doménami založené na prohlížeči klientů pro sdílení obsahu.  
-   [JSONP](api-management-cross-domain-policies.md#JSONP) – přidá JSON s podporou odsazení (JSONP) pro operaci nebo rozhraní API pro volání mezi doménami založené na prohlížeči klientů JavaScript povolit.  
  
##  <a name="AllowCrossDomainCalls"></a> Povolit volání mezi doménami  
 Použití `cross-domain` zásad zpřístupnit rozhraní API z Adobe Flash a Silverlight Microsoftu založené na prohlížeči klientů.  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<cross-domain>  
   <!-Policy configuration is in the Adobe cross-domain policy file format,   
      see http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->  
</cross-domain>  
```  
  
### <a name="example"></a>Příklad:  
  
```xml  
<cross-domain>  
    <cross-domain-policy>  
        <allow-http-request-headers-from domain='*' headers='*' />  
    </cross-domain-policy>  
</cross-domain>  
```  
  
### <a name="elements"></a>Elementy  
  
|Název|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|mezi doménami|Kořenový element. Podřízené elementy musí odpovídat [specifikace souboru Adobe zásady mezi doménami](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Ano|  
  
### <a name="usage"></a>Využití  
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásad:** příchozí  
-   **Zásady obory:** globální  
  
##  <a name="CORS"></a> CORS  
 `cors` Zásad přidá prostředků mezi zdroji (CORS) podporu o operaci nebo rozhraní API pro povolit volání mezi doménami založené na prohlížeči klientů pro sdílení obsahu.  
  
 CORS umožňuje jenom prohlížeč a server interakce a zjistěte, jestli se mají povolit konkrétní požadavky mezi zdroji (například XMLHttpRequests volání z jazyka JavaScript na webové stránce do jiných domén). To umožňuje více flexibility než povolíte jen pro stejné požadavky, ale je bezpečnější než povolíte všechny požadavky mezi zdroji.  
  
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
  
### <a name="example"></a>Příklad:  
 Tento příklad ukazuje, jak podporovat přípravné požadavků, například ty, které mají vlastní hlavičky nebo jiných metod než GET a POST. Chcete-li podporovat vlastní hlavičky a dalších příkazů HTTP, použijte `allowed-methods` a `allowed-headers` sections – jak je znázorněno v následujícím příkladu.  
  
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
|cors|Kořenový element.|Ano|neuvedeno|  
|Povolené zdroje|Obsahuje `origin` prvky, které popisují povolené zdroje pro požadavky napříč doménami. `allowed-origins` může obsahovat buď jeden `origin` prvek, který určuje `*` umožňující jakýkoli původ nebo jeden nebo více `origin` prvky, které obsahují identifikátoru URI.|Ano|neuvedeno|  
|počátek|Hodnota může být buď `*` povolit všechny původy nebo identifikátor URI, který určuje jeden počátek. Identifikátor URI musí obsahovat schéma, hostitele a port.|Ano|Pokud port není uveden v identifikátoru URI, používá port 80 pro protokol HTTP a používá port 443 pro protokol HTTPS.|  
|povolené metody|Tento prvek je nutný, pokud metody jiné než GET nebo POST jsou povoleny. Obsahuje `method` elementy, které určují podporované příkazy HTTP.|Ne|Pokud tento oddíl není k dispozici, jsou podporovány GET a POST.|  
|method|Určuje příkaz HTTP.|Alespoň jeden `method` element je povinný, pokud `allowed-methods` oddílu je k dispozici.|neuvedeno|  
|povolené hlavičky|Tento prvek obsahuje `header` elementy názvy záhlaví, které mohou být součástí požadavku.|Ne|neuvedeno|  
|expose-headers.|Tento prvek obsahuje `header` elementy názvy záhlaví, které budou přístupné pro klienta.|Ne|neuvedeno|  
|záhlaví|Určuje název hlavičky.|Alespoň jeden `header` je vyžadován prvek v `allowed-headers` nebo `expose-headers` oddíl je k dispozici.|neuvedeno|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|Povolit přihlašovací údaje|`Access-Control-Allow-Credentials` Záhlaví v předběžné odpovědi se nastaví na hodnotu tohoto atributu a mít vliv na schopnost klienta přihlašovacích údajů napříč doménami požadavků na odeslání.|Ne|false (nepravda)|  
|zaregistrovat k předběžné verzi výsledek max-age|`Access-Control-Max-Age` Záhlaví v předběžné odpovědi se nastaví na hodnotu tohoto atributu a mít vliv na schopnost uživatelský agent odpověď přípravné mezipaměti.|Ne|0|  
  
### <a name="usage"></a>Využití  
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásad:** příchozí  
-   **Zásady obory:** rozhraní API, operace  
  
##  <a name="JSONP"></a> JSONP  
 `jsonp` Zásad přidá JSON s podporou odsazení (JSONP) pro operaci nebo rozhraní API pro volání mezi doménami založené na prohlížeči klientů JavaScript povolit. JSONP je metoda použita v programech jazyka JavaScript požadovat data ze serveru v jiné doméně. JSONP obchází omezení vynucená většina webových prohlížečů, ve kterém přístup k webovým stránkám musí být ve stejné doméně.  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<jsonp callback-parameter-name="callback function name" />  
```  
  
### <a name="example"></a>Příklad:  
  
```xml  
<jsonp callback-parameter-name="cb" />  
```  
  
 Při volání metody bez parametru zpětného volání? cb = XXX vrátí prostý JSON (bez Obálka volání funkce).  
  
 Pokud chcete přidat parametr zpětného volání `?cb=XXX` vrátí výsledek JSONP, původní JSON pro zabalení jako výsledků v požadovaném funkce zpětného volání `XYZ('<json result goes here>');`  
  
### <a name="elements"></a>Elementy  
  
|Název|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|JSONP|Kořenový element.|Ano|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|Název parametru zpětného volání|Mezi doménami JavaScript volání funkce s předponou plně kvalifikovaný název domény ve kterém se funkce nachází.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásad:** odchozí  
-   **Zásady obory:** globální, produktu, rozhraní API, operace  
  
## <a name="next-steps"></a>Další postup

Práce se zásadami pro další informace najdete v tématu:

+ [Zásady ve službě API Management](api-management-howto-policies.md)
+ [Transformujte rozhraní API](transform-api.md)
+ [Referenční příručce o zásadách](api-management-policy-reference.md) úplný seznam zásad příkazy a jejich nastavení
+ [Ukázky zásad](policy-samples.md)   