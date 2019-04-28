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
ms.openlocfilehash: c0f8da779ca656cf357c418b8766a53307643695
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764342"
---
# <a name="api-management-authentication-policies"></a>Zásady ověřování služby API Management
Toto téma obsahuje odkaz pro následující zásady služby API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady ve službě API Management](https://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AuthenticationPolicies"></a> Zásady ověřování  
  
-   [Ověřování pomocí Basic](api-management-authentication-policies.md#Basic) – ověřování pomocí back-end službu pomocí základního ověřování.  
  
-   [Ověřování pomocí certifikátu klienta](api-management-authentication-policies.md#ClientCertificate) – ověřování pomocí služby back-end pomocí klientských certifikátů.  

-   [Ověřování pomocí spravované identity](api-management-authentication-policies.md#ManagedIdentity) – ověřování pomocí [se identita spravované](../active-directory/managed-identities-azure-resources/overview.md) pro službu API Management.  
  
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
|authentication-basic|Kořenový element.|Ano|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|uživatelské jméno|Určuje uživatelské jméno základní přihlašovací údaje.|Ano|neuvedeno|  
|password|Určuje heslo základní přihlašovací údaje.|Ano|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásad:** příchozí  
  
-   **Obory zásad:** Rozhraní API  
  
##  <a name="ClientCertificate"></a> Ověřování pomocí certifikátu klienta  
 Použití `authentication-certificate` zásad ověřování s back-endové služby pomocí klientského certifikátu. Certifikát se musí být [nainstalováno do API managementu](https://go.microsoft.com/fwlink/?LinkID=511599) první a je identifikován jeho kryptografický otisk.  
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>  
```  
  
### <a name="examples"></a>Příklady  
  
V tomto příkladu klientovi certifikátu je identifikován jeho kryptografický otisk.
```xml  
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />  
``` 
V tomto příkladu se klientský certifikát identifikovat podle názvu prostředku.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Elementy  
  
|Název|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|authentication-certificate|Kořenový element.|Ano|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|thumbprint|Kryptografický otisk certifikátu klienta.|Buď `thumbprint` nebo `certificate-id` musí být k dispozici.|neuvedeno|  
|id certifikátu|Název prostředku certifikátu.|Buď `thumbprint` nebo `certificate-id` musí být k dispozici.|neuvedeno|  
  
### <a name="usage"></a>Využití  
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásad:** příchozí  
  
-   **Obory zásad:** Rozhraní API  

##  <a name="ManagedIdentity"></a> Ověřování pomocí spravované identity  
 Použití `authentication-managed-identity` zásad ověřování s využitím služby back-end pomocí spravované identity služby API Management. Efektivní ochranu nastavují pomocí spravované identity k získání přístupového tokenu z Azure Active Directory pro přístup k zadaný prostředek. 
  
### <a name="policy-statement"></a>Prohlášení o zásadách  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Příklad:  
  
```xml  
<authentication-managed-identity resource="https://graph.windows.net" output-token-variable-name="test-access-token" ignore-error="true" /> 
```  
  
### <a name="elements"></a>Elementy  
  
|Název|Popis|Požaduje se|  
|----------|-----------------|--------------|  
|authentication-managed-identity |Kořenový element.|Ano|  
  
### <a name="attributes"></a>Atributy  
  
|Název|Popis|Požaduje se|Výchozí|  
|----------|-----------------|--------------|-------------|  
|prostředek|řetězec. Identifikátor URI ID aplikace z cílové webové rozhraní API (zabezpečené prostředků) v Azure Active Directory.|Ano|neuvedeno|  
|výstup token proměnnou název|řetězec. Název kontextové proměnné, která se zobrazí hodnota tokenu jako typ objektu, který `string`.|Ne|neuvedeno|  
|ignore-error|Datový typ Boolean. Pokud nastavit `true`, kanál zásady budou i nadále spouštět i v případě, že se získat přístupový token.|Ne|false (nepravda)|  
  
### <a name="usage"></a>Využití  
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Části zásad:** příchozí  
  
-   **Zásady obory:** globální, produktu, rozhraní API, operace  

## <a name="next-steps"></a>Další postup
Práce se zásadami pro další informace najdete v tématu:

+ [Zásady ve službě API Management](api-management-howto-policies.md)
+ [Transformujte rozhraní API](transform-api.md)
+ [Referenční příručce o zásadách](api-management-policy-reference.md) úplný seznam zásad příkazy a jejich nastavení
+ [Ukázky zásad](policy-samples.md)   
