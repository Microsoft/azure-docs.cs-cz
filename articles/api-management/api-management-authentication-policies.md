---
title: Zásady ověřování v Azure API Management | Microsoft Docs
description: Přečtěte si o zásadách ověřování, které jsou k dispozici pro použití v Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 69584b434ac0442df48dcdea2a7d9f2aca9c1ccd
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073743"
---
# <a name="api-management-authentication-policies"></a>API Management zásady ověřování
V tomto tématu najdete referenční informace pro následující zásady API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady v API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="AuthenticationPolicies"></a>Zásady ověřování

-   [](api-management-authentication-policies.md#Basic) Ověřování pomocí služby back-end s využitím základního ověřování pomocí back-endu

-   [Ověřování pomocí klientského certifikátu](api-management-authentication-policies.md#ClientCertificate) – ověřování pomocí back-end služby pomocí klientských certifikátů.

-   [Ověřování pomocí spravované identity](api-management-authentication-policies.md#ManagedIdentity) – ověřování pomocí [spravované identity](../active-directory/managed-identities-azure-resources/overview.md) pro službu API Management.

##  <a name="Basic"></a>Ověřování s využitím úrovně Basic
 `authentication-basic` Použijte zásadu k ověřování pomocí back-end služby pomocí základního ověřování. Tato zásada efektivně nastaví hlavičku autorizace protokolu HTTP na hodnotu odpovídající přihlašovacím údajům uvedeným v zásadách.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Příklad

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Elementy

|Name|Popis|Požadováno|
|----------|-----------------|--------------|
|ověřování – základní|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Name|Popis|Požadováno|Výchozí|
|----------|-----------------|--------------|-------------|
|username|Určuje uživatelské jméno základního přihlašovacího údaje.|Ano|Není k dispozici|
|password|Určuje heslo základního přihlašovacího údaje.|Ano|Není k dispozici|

### <a name="usage"></a>Použití
 Tyto zásady se dají použít v následujících oddílech [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zásad.

-   **Oddíly zásad:** příchozí

-   **Obory zásad:** všechny rozsahy

##  <a name="ClientCertificate"></a>Ověřování pomocí klientského certifikátu
 Pomocí této `authentication-certificate` zásady můžete ověřit pomocí back-end služby pomocí klientského certifikátu. Certifikát je potřeba nainstalovat nejdřív [do API Management](https://go.microsoft.com/fwlink/?LinkID=511599) a je identifikovaný jeho kryptografickým otiskem.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Příklady

V tomto příkladu je klientský certifikát identifikovaný jeho kryptografickým otiskem.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
V tomto ukázkovém klientském certifikátu je identifikován název prostředku.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Elementy  
  
|Name|Popis|Požadováno|  
|----------|-----------------|--------------|  
|ověřování – certifikát|Kořenový element.|Ano|  
  
### <a name="attributes"></a>Atributy  
  
|Name|Popis|Požadováno|Výchozí|  
|----------|-----------------|--------------|-------------|  
|thumbprint|Kryptografický otisk pro klientský certifikát|Musí `thumbprint` být `certificate-id` přítomen buď nebo.|Není k dispozici|  
|ID certifikátu|Název prostředku certifikátu.|Musí `thumbprint` být `certificate-id` přítomen buď nebo.|Není k dispozici|  
  
### <a name="usage"></a>Použití  
 Tyto zásady se dají použít v následujících oddílech [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zásad.  
  
-   **Oddíly zásad:** příchozí  
  
-   **Obory zásad:** všechny rozsahy  

##  <a name="ManagedIdentity"></a>Ověřování pomocí spravované identity  
 Pomocí této `authentication-managed-identity` zásady můžete ověřit pomocí back-end služby pomocí spravované identity služby API Management. Tato zásada v podstatě používá spravovanou identitu k získání přístupového tokenu z Azure Active Directory pro přístup k zadanému prostředku. Po úspěšném získání tokenu zásada nastaví hodnotu tokenu v `Authorization` záhlaví `Bearer` pomocí schématu.
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Příklad  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Použití spravované identity k ověřování pomocí back-end služby
```xml  
<authentication-managed-identity resource="https://graph.windows.net"/> 
```
  
#### <a name="use-managed-identity-in-send-request-policy"></a>Použití spravované identity v zásadách odeslání – požadavek
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Elementy  
  
|Name|Popis|Požadováno|  
|----------|-----------------|--------------|  
|ověřování – spravovaná identita |Kořenový element.|Ano|  
  
### <a name="attributes"></a>Atributy  
  
|Name|Popis|Požadováno|Výchozí|  
|----------|-----------------|--------------|-------------|  
|resource|Řetezce. Identifikátor URI ID aplikace cílového webového rozhraní API (zabezpečeného prostředku) v Azure Active Directory.|Ano|Není k dispozici|  
|výstup-token-proměnná-Name|Řetezce. Název kontextové proměnné, která obdrží hodnotu tokenu jako typ `string`objektu. |Ne|Není k dispozici|  
|ignorovat – chyba|Datového. Pokud se nastaví `true`na, kanál zásad se bude dál spouštět i v případě, že se přístupový token nezíská.|Ne|false|  
  
### <a name="usage"></a>Použití  
 Tyto zásady se dají použít v následujících oddílech [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zásad.  
  
-   **Oddíly zásad:** příchozí  
  
-   **Obory zásad:** všechny rozsahy

## <a name="next-steps"></a>Další postup
Další informace o práci se zásadami najdete v těchto tématech:

+ [Zásady v API Management](api-management-howto-policies.md)
+ [Transformační rozhraní API](transform-api.md)
+ [Odkaz na zásady](api-management-policy-reference.md) pro úplný seznam příkazů zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)
