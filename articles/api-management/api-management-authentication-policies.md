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
ms.date: 01/27/2021
ms.author: apimpm
ms.openlocfilehash: 22d2960801cac2222f868c384a55b4bf436bc75b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492599"
---
# <a name="api-management-authentication-policies"></a>Zásady ověřování ve službě API Management
V tomto tématu najdete referenční informace pro následující zásady API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady v API Management](./api-management-policies.md).

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a> Zásady ověřování

-   Ověřování pomocí služby [back-end](api-management-authentication-policies.md#Basic) s využitím základního ověřování pomocí back-endu

-   [Ověřování pomocí klientského certifikátu](api-management-authentication-policies.md#ClientCertificate) – ověřování pomocí back-end služby pomocí klientských certifikátů.

-   [Ověřování pomocí spravované identity](api-management-authentication-policies.md#ManagedIdentity) – ověřování pomocí [spravované identity](../active-directory/managed-identities-azure-resources/overview.md) pro službu API Management.

##  <a name="authenticate-with-basic"></a><a name="Basic"></a> Ověřování s využitím úrovně Basic
 Použijte `authentication-basic` zásadu k ověřování pomocí back-end služby pomocí základního ověřování. Tato zásada efektivně nastaví hlavičku autorizace protokolu HTTP na hodnotu odpovídající přihlašovacím údajům uvedeným v zásadách.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Příklad

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Elementy

|Název|Popis|Povinné|
|----------|-----------------|--------------|
|ověřování – základní|Kořenový element.|Yes|

### <a name="attributes"></a>Atributy

|Název|Popis|Povinné|Výchozí|
|----------|-----------------|--------------|-------------|
|username|Určuje uživatelské jméno základního přihlašovacího údaje.|Yes|–|
|heslo|Určuje heslo základního přihlašovacího údaje.|Yes|–|

### <a name="usage"></a>Využití
 Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí

-   **Obory zásad:** všechny rozsahy

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a> Ověřování pomocí klientského certifikátu
 Pomocí této `authentication-certificate` zásady můžete ověřit pomocí back-end služby pomocí klientského certifikátu. Certifikát se musí nejdřív [nainstalovat do API Management](./api-management-howto-mutual-certificates.md) a identifikuje ho jeho kryptografický otisk nebo ID certifikátu (název prostředku). 

> [!CAUTION]
> Pokud certifikát odkazuje na certifikát uložený v Azure Key Vault, identifikujte ho pomocí ID certifikátu. Když se certifikát trezoru klíčů otočí, dojde ke změně jeho kryptografického otisku v API Management a tato zásada nerozpozná nový certifikát, pokud je identifikovaný kryptografickým otiskem.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Příklady

V tomto příkladu je klientský certifikát identifikovaný ID certifikátu:

```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
``` 

V tomto příkladu je klientský certifikát identifikovaný pomocí jeho kryptografického otisku:

```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
V tomto příkladu se klientský certifikát nastaví v zásadách, ale nenačtený z integrovaného úložiště certifikátů:

```xml
<authentication-certificate body="@(context.Variables.GetValueOrDefault<byte[]>("byteCertificate"))" password="optional-certificate-password" />
```

### <a name="elements"></a>Elementy  
  
|Název|Popis|Povinné|  
|----------|-----------------|--------------|  
|ověřování – certifikát|Kořenový element.|Yes|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Povinné|Výchozí|  
|----------|-----------------|--------------|-------------|  
|kryptografický|Kryptografický otisk pro klientský certifikát|`thumbprint` `certificate-id` Musí být přítomen buď nebo.|–|
|ID certifikátu|Název prostředku certifikátu.|`thumbprint` `certificate-id` Musí být přítomen buď nebo.|–|
|text|Certifikát klienta jako bajtové pole.|No|–|
|heslo|Heslo pro klientský certifikát.|Používá se, pokud je certifikát určený v nástroji `body` chráněný heslem.|–|
  
### <a name="usage"></a>Využití  
 Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.  
  
-   **Oddíly zásad:** příchozí  
  
-   **Obory zásad:** všechny rozsahy  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a> Ověřování pomocí spravované identity  
 Pomocí této `authentication-managed-identity` zásady můžete ověřit službu back-end pomocí spravované identity. Tato zásada v podstatě používá spravovanou identitu k získání přístupového tokenu z Azure Active Directory pro přístup k zadanému prostředku. Po úspěšném získání tokenu zásada nastaví hodnotu tokenu v `Authorization` záhlaví pomocí `Bearer` schématu.

K vyžádání tokenu se dá použít jak identita přiřazená systémem, tak i kterákoli z více uživatelsky přiřazených identit. Pokud není `client-id` zadána identita přiřazená systémem, předpokládá se. Pokud je k `client-id` této identitě přiřazena proměnná, je vyžadována pro tuto identitu uživatele z Azure Active Directory
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<authentication-managed-identity resource="resource" client-id="clientid of user-assigned identity" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Příklad  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Použití spravované identity k ověřování pomocí back-end služby
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
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Bus-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"/> <!--Your own Azure AD Application-->
```

#### <a name="use-managed-identity-and-set-header-manually"></a>Použijte spravovanou identitu a nastavte záhlaví ručně.

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"
   output-token-variable-name="msi-access-token" ignore-error="false" /> <!--Your own Azure AD Application-->
<set-header name="Authorization" exists-action="override">
   <value>@("Bearer " + (string)context.Variables["msi-access-token"])</value>
</set-header>
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
  
|Název|Popis|Povinné|  
|----------|-----------------|--------------|  
|ověřování – spravovaná identita |Kořenový element.|Yes|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Povinné|Výchozí|  
|----------|-----------------|--------------|-------------|  
|prostředek|Řetězec. ID aplikace cílového webového rozhraní API (zabezpečeného prostředku) v Azure Active Directory.|Yes|–|
|ID klienta|Řetězec. ID aplikace identity přiřazené uživatelem v Azure Active Directory.|No|Identita přiřazená systémem|
|výstup-token-proměnná-Name|Řetězec. Název kontextové proměnné, která obdrží hodnotu tokenu jako typ objektu `string` . |No|–|  
|ignorovat – chyba|Datového. Pokud se nastaví na `true` , kanál zásad se bude dál spouštět i v případě, že se přístupový token nezíská.|No|false (nepravda)|  
  
### <a name="usage"></a>Využití  
 Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.  
  
-   **Oddíly zásad:** příchozí  
  
-   **Obory zásad:** všechny rozsahy

## <a name="next-steps"></a>Další kroky
Další informace o práci se zásadami najdete v těchto tématech:

+ [Zásady v API Management](api-management-howto-policies.md)
+ [Transformační rozhraní API](transform-api.md)
+ [Odkaz na zásady](./api-management-policies.md) pro úplný seznam příkazů zásad a jejich nastavení
+ [Ukázky zásad](./policy-reference.md)