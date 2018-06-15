---
title: Zásady ověřování Azure API Management | Microsoft Docs
description: Další informace o zásady ověřování, která je k dispozici pro použití v Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 4d13d9dbea9da9db5bfe9a9af85fdbf9eab1ae84
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2017
ms.locfileid: "26127755"
---
# <a name="api-management-authentication-policies"></a>Zásady ověřování služby API Management
Toto téma obsahuje odkaz pro následující zásady služby API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady ve službě API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AuthenticationPolicies"></a>Zásady ověřování  
  
-   [Ověřování Basic](api-management-authentication-policies.md#Basic) -ověření pomocí back-end službu pomocí základního ověřování.  
  
-   [Ověřování pomocí certifikátu klienta](api-management-authentication-policies.md#ClientCertificate) -ověření pomocí back-end službu pomocí klientských certifikátů.  
  
##  <a name="Basic"></a>Ověřování Basic  
 Použití `authentication-basic` zásad k ověření s back-end službu pomocí základního ověřování. Tato zásada nastavuje hlavičku autorizace protokolu HTTP na hodnotu odpovídající přihlašovacích údajů zadaných v zásadách.  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<authentication-basic username="username" password="password" />  
```  
  
### <a name="example"></a>Příklad  
  
```xml  
<authentication-basic username="testuser" password="testpassword" />  
```  
  
### <a name="elements"></a>Elementy  
  
|Name (Název)|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|ověřování – základní|Kořenový element.|Ano|  
  
### <a name="attributes"></a>Atributy  
  
|Name (Název)|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|uživatelské jméno|Určuje uživatelské jméno základní přihlašovací údaje.|Ano|Není k dispozici|  
|heslo|Určuje heslo základní pověření.|Ano|Není k dispozici|  
  
### <a name="usage"></a>Využití  
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásady:** příchozí  
  
-   **Zásady obory:** rozhraní API  
  
##  <a name="ClientCertificate"></a>Ověřování pomocí certifikátu klienta  
 Použití `authentication-certificate` zásad k ověření s back-end službu pomocí klientského certifikátu. Certifikát musí být [nainstalován do rozhraní API správy](http://go.microsoft.com/fwlink/?LinkID=511599) první a je určený podle jeho kryptografický otisk.  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<authentication-certificate thumbprint="thumbprint" />  
```  
  
### <a name="example"></a>Příklad  
  
```xml  
<authentication-certificate thumbprint="....." />  
```  
  
### <a name="elements"></a>Elementy  
  
|Name (Název)|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|ověřovací certifikát|Kořenový element.|Ano|  
  
### <a name="attributes"></a>Atributy  
  
|Name (Název)|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|Kryptografický otisk|Kryptografický otisk certifikátu klienta.|Ano|Není k dispozici|  
  
### <a name="usage"></a>Využití  
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásady:** příchozí  
  
-   **Zásady obory:** rozhraní API  
  

## <a name="next-steps"></a>Další kroky
Práce se zásadami pro další informace najdete v tématu:

+ [Zásady ve službě API Management](api-management-howto-policies.md)
+ [Transformuje rozhraní API](transform-api.md)
+ [Referenční informace o zásadách](api-management-policy-reference.md) pro úplný seznam příkazy zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)   
