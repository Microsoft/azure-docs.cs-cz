---
title: Zásady ověřování azure api managementu | Dokumenty společnosti Microsoft
description: Přečtěte si o zásadách ověřování, které jsou dostupné pro použití ve správě rozhraní Azure API.
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
ms.openlocfilehash: 828f738ff8923dc8194e2449f5fb0be74ef45ad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473553"
---
# <a name="api-management-authentication-policies"></a>Zásady ověřování ve službě API Management
Toto téma obsahuje odkaz na následující zásady správy rozhraní API. Informace o přidávání a konfiguraci zásad naleznete [v tématu Zásady ve správě rozhraní API](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a>Zásady ověřování

-   [Ověření pomocí základní](api-management-authentication-policies.md#Basic) - ověření pomocí back-endové služby pomocí základního ověřování.

-   [Ověření pomocí klientského certifikátu](api-management-authentication-policies.md#ClientCertificate) – Ověření pomocí back-endové služby pomocí klientských certifikátů.

-   [Ověření pomocí spravované identity](api-management-authentication-policies.md#ManagedIdentity) – Ověření [spravovanou identitou](../active-directory/managed-identities-azure-resources/overview.md) pro službu API Management.

##  <a name="authenticate-with-basic"></a><a name="Basic"></a>Ověření pomocí základní
 Pomocí `authentication-basic` této zásady se můžete ověřit pomocí back-endové služby pomocí základního ověřování. Tato zásada efektivně nastaví hlavičku autorizace PROTOKOLU HTTP na hodnotu odpovídající pověřením poskytnutým v zásadě.

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
|autentizace -basic|Kořenový prvek.|Ano|

### <a name="attributes"></a>Atributy

|Name (Název)|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|uživatelské jméno|Určuje uživatelské jméno základního pověření.|Ano|Není dostupné.|
|heslo|Určuje heslo základního pověření.|Ano|Není dostupné.|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí

-   **Obory zásad:** všechny obory

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a>Ověření pomocí klientského certifikátu
 Pomocí `authentication-certificate` této zásady se můžete ověřit pomocí back-endové služby pomocí klientského certifikátu. Certifikát musí být [nejprve nainstalován do správy rozhraní API](https://go.microsoft.com/fwlink/?LinkID=511599) a je identifikován jeho kryptografickým otiskem.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Příklady

V tomto příkladu je klientský certifikát identifikován jeho kryptografickým otiskem.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
V tomto příkladu je klientský certifikát identifikován názvem prostředku.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Elementy  
  
|Name (Název)|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|ověřovací certifikát|Kořenový prvek.|Ano|  
  
### <a name="attributes"></a>Atributy  
  
|Name (Název)|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|Miniatura|Kryptografický otisk klientského certifikátu.|Buď `thumbprint` `certificate-id` nebo musí být přítomen.|Není dostupné.|  
|id certifikátu|Název prostředku certifikátu.|Buď `thumbprint` `certificate-id` nebo musí být přítomen.|Není dostupné.|  
  
### <a name="usage"></a>Využití  
 Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Oddíly zásad:** příchozí  
  
-   **Obory zásad:** všechny obory  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a>Ověření pomocí spravované identity  
 Pomocí `authentication-managed-identity` této zásady se můžete ověřovat pomocí back-endové služby pomocí spravované identity služby API Management. Tato zásada v podstatě používá spravovanou identitu k získání přístupového tokenu ze služby Azure Active Directory pro přístup k zadanému prostředku. Po úspěšném získání tokenu zásada nastaví hodnotu `Authorization` tokenu `Bearer` v záhlaví pomocí schématu.
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Příklad  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Použití spravované identity k ověření pomocí back-endové služby
```xml  
<authentication-managed-identity resource="https://graph.microsoft.com"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Busr-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```
  
#### <a name="use-managed-identity-in-send-request-policy"></a>Použití spravované identity v zásadách odeslání žádosti o odeslání
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Elementy  
  
|Name (Název)|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|identita spravovaná ověřováním |Kořenový prvek.|Ano|  
  
### <a name="attributes"></a>Atributy  
  
|Name (Název)|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|prostředek|Řetězec. ID aplikace cílového webového rozhraní API (zabezpečeného prostředku) ve službě Azure Active Directory.|Ano|Není dostupné.|  
|output-token-variable-name|Řetězec. Název kontextové proměnné, která obdrží hodnotu `string`tokenu jako typ objektu . |Ne|Není dostupné.|  
|chyba ignorování|Boolean. Pokud je `true`nastavena na , bude kanál zásad nadále spouštět i v případě, že není získán přístupový token.|Ne|false (nepravda)|  
  
### <a name="usage"></a>Využití  
 Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Oddíly zásad:** příchozí  
  
-   **Obory zásad:** všechny obory

## <a name="next-steps"></a>Další kroky
Další informace o práci se zásadami naleznete v tématu:

+ [Zásady ve správě rozhraní API](api-management-howto-policies.md)
+ [Transformovat api](transform-api.md)
+ [Odkaz na zásady](api-management-policy-reference.md) pro úplný seznam prohlášení zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)
