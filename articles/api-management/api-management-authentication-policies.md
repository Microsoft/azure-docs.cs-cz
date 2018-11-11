---
title: Zásady ověřování služby Azure API Management | Dokumentace Microsoftu
description: Další informace o zásadách ověřování k dispozici pro použití ve službě Azure API Management.
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
ms.openlocfilehash: 4c4c03fffa5786bf3a50f4d2c03511f0a2de0f48
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250947"
---
# <a name="api-management-authentication-policies"></a>Zásady ověřování služby API Management
Toto téma obsahuje odkaz pro následující zásady služby API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady ve službě API Management](https://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AuthenticationPolicies"></a> Zásady ověřování  
  
-   [Ověřování pomocí Basic](api-management-authentication-policies.md#Basic) – ověřování pomocí back-end službu pomocí základního ověřování.  
  
-   [Ověřování pomocí certifikátu klienta](api-management-authentication-policies.md#ClientCertificate) – ověřování pomocí služby back-end pomocí klientských certifikátů.  
  
##  <a name="Basic"></a> Ověřování pomocí Basic  
 Použití `authentication-basic` zásad ověřování s back-end službu pomocí základního ověřování. Tato zásada nastavuje na hodnotu odpovídající přihlašovací údaje zadané v zásadách HTTP autorizační hlavičky.  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<authentication-basic username="username" password="password" />  
```  
  
### <a name="example"></a>Příklad:  
  
```xml  
<authentication-basic username="testuser" password="testpassword" />  
```  
  
### <a name="elements"></a>Elementy  
  
|Název|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|ověřování – basic|Kořenový element.|Ano|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|uživatelské jméno|Určuje uživatelské jméno základní přihlašovací údaje.|Ano|neuvedeno|  
|heslo|Určuje heslo základní přihlašovací údaje.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásad:** příchozí  
  
-   **Zásady obory:** rozhraní API  
  
##  <a name="ClientCertificate"></a> Ověřování pomocí certifikátu klienta  
 Použití `authentication-certificate` zásad ověřování s back-endové služby pomocí klientského certifikátu. Certifikát se musí být [nainstalováno do API managementu](https://go.microsoft.com/fwlink/?LinkID=511599) první a je identifikován jeho kryptografický otisk.  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<authentication-certificate thumbprint="thumbprint" />  
```  
  
### <a name="example"></a>Příklad:  
  
```xml  
<authentication-certificate thumbprint="....." />  
```  
  
### <a name="elements"></a>Elementy  
  
|Název|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|certifikát pro ověřování|Kořenový element.|Ano|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|Kryptografický otisk|Kryptografický otisk certifikátu klienta.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásad:** příchozí  
  
-   **Zásady obory:** rozhraní API  
  

## <a name="next-steps"></a>Další postup
Práce se zásadami pro další informace najdete v tématu:

+ [Zásady ve službě API Management](api-management-howto-policies.md)
+ [Transformujte rozhraní API](transform-api.md)
+ [Referenční příručce o zásadách](api-management-policy-reference.md) úplný seznam zásad příkazy a jejich nastavení
+ [Ukázky zásad](policy-samples.md)   
