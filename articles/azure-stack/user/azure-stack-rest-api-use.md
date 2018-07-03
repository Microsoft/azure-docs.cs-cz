---
title: Použití rozhraní API Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak načíst ověření z Azure, aby žádosti rozhraní API pro Azure Stack.
services: azure-stack
documentationcenter: ''
author: cblackuk
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/02/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 3b89564bf17a9884640b51faa1c3966dce93f89a
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346786"
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="use-the-azure-stack-api"></a>Použití rozhraní API v Azure stacku

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Rozhraní API (Application Programming) můžete použít k automatizaci operací, jako je přidání virtuálního počítače do cloudu služby Azure Stack.

Rozhraní API vyžaduje váš klient k ověření na koncový bod přihlašovacího účtu Microsoft Azure. Koncový bod vrátí token určený v hlavičce každého požadavku odeslaného do rozhraní API Azure Stack. Microsoft Azure používá Oauth 2.0.

Tento článek obsahuje příklady, které používají **cURL** nástroj k vytváření žádostí o služby Azure Stack. Použití cURL, je nástroj příkazového řádku pomocí knihovny pro přenos dat. Tyto příklady provedou proces načítání tokenu pro přístup k rozhraní API služby Azure Stack. Většina programovacích jazyků poskytují knihovny Oauth 2.0, které mají robustní token úkolů správy a zpracování těchto obnovení tokenu.

Projděte si celý proces pomocí REST API služby Azure Stack pomocí obecného klienta REST, například **cURL**, které vám pomohou pochopit základní požadavky a ukazuje, co můžete očekávat přijímat v datové části odpovědi.

Tento článek není prozkoumejte všechny možnosti, které jsou k dispozici pro získávání tokenů, jako je například interaktivní přihlášení nebo vytváření vyhrazené ID aplikace. Chcete-li získat informace o těchto tématech najdete v článku [Reference k rozhraní Azure REST API](https://docs.microsoft.com/rest/api/).

## <a name="get-a-token-from-azure"></a>Získání tokenu z Azure

Vytvořte hlavní část žádosti formátována pomocí typ obsahu x-www-form-urlencoded sloužící k získání přístupového tokenu. POŠLE požadavek na koncový bod REST ověřování Azure a přihlaste se.

### <a name="uri"></a>URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**ID tenanta** je buď:

 - Vaše doména tenanta, jako například `fabrikam.onmicrosoft.com`
 - ID vašeho tenanta, jako například `8eaed023-2b34-4da1-9baa-8bc8c9d6a491`
 - Výchozí hodnota pro klíče tenanta nezávislé: `common`

### <a name="post-body"></a>Tělo POST

```bash  
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

Pro každou hodnotu:

 - **Parametr grant_type**  
    Typ schématu ověřování budete používat. V tomto příkladu je hodnota `password`

 - **prostředek**  
    Prostředek přistupuje k tokenu. Najít prostředek pomocí dotazu na koncový bod metadat pro správu služby Azure Stack. Podívejte se na **cílovým skupinám na místě** oddílu

 - **Koncový bod pro správu Azure Stack**  
    ```
    https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
    ```

  > [!NOTE]  
  > Pokud jste správcem pokusu o přístup k rozhraní API klienta pak můžete musí nezapomeňte použít koncový bod tenanta, například: `https://adminmanagement.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-011`  

  Například v Azure Stack Development Kit jako koncový bod:

    ```bash
    curl 'https://management.local.azurestack.external/metadata/endpoints?api-version=2015-01-01'
    ```

  Odpověď:

  ```
  {
  "galleryEndpoint":"https://adminportal.local.azurestack.external:30015/",
  "graphEndpoint":"https://graph.windows.net/",
  "portalEndpoint":"https://adminportal.local.azurestack.external/",
  "authentication":{
      "loginEndpoint":"https://login.windows.net/",
      "audiences":["https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"]
      }
  }
  ```

### <a name="example"></a>Příklad:

  ```
  https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
  ```

  **client_id**

  Tato hodnota je pevně nastavená na výchozí hodnotu:

  ```
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  Alternativní možnosti jsou k dispozici pro konkrétní scénáře:

  
  | Aplikace | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

  **uživatelské jméno**

  Například účet služby Azure Stack AAD:

  ```
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

  **Heslo**

  Heslo správce služby Azure Stack AAD.

### <a name="example"></a>Příklad:

Žádost:

```
curl -X "POST" "https://login.windows.net/fabrikam.onmicrosoft.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=1950a258-227b-4e31-a9cf-717495945fc2" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=admin@fabrikam.onmicrosoft.com" \
--data-urlencode 'password=Password12345' \
--data-urlencode "resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"
```

Odpověď:

```
{
  "token_type": "Bearer",
  "scope": "user_impersonation",
  "expires_in": "3599",
  "ext_expires_in": "0",
  "expires_on": "1512574780",
  "not_before": "1512570880",
  "resource": "https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155",
  "access_token": "eyJ0eXAiOi...truncated for readability..."
}
```

## <a name="api-queries"></a>Dotazů na rozhraní API

Jakmile se zobrazí váš přístupový token, musíte přidat jako hlavičku do všech žádostí o rozhraní API. Pokud chcete udělat, je potřeba vytvořit hlavičku **autorizace** s hodnotou: `Bearer <access token>`. Příklad:

Žádost:

```bash  
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

Odpověď:

```bash  
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>Adresa URL struktury a syntaxe dotazů

Žádost o obecný identifikátor URI, se skládá z: {Schéma identifikátoru URI} :// {identifikátor URI-host} / {resource-path}? {řetězec dotazu}

- **Schéma identifikátoru URI**:  
Identifikátor URI označuje protokol použitý k odeslání žádosti. Například `http` nebo `https`.
- **Hostitel identifikátoru URI**:  
Hostitele Určuje název domény nebo IP adresu serveru koncový bod služby REST je hostitelem, jako například `graph.microsoft.com` nebo `adminmanagement.local.azurestack.external`.
- **Cesta k prostředku**:  
Cesta Určuje prostředek nebo kolekci prostředků, které mohou zahrnovat více segmentů používaných službou při určování výběru těchto prostředků. Příklad: `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` můžete použít k dotazování v seznamu vlastníků konkrétní aplikace v rámci kolekce aplikací.
- **Řetězec dotazu**:  
Řetězec poskytuje další jednoduché parametry, jako je například kritéria výběru verze nebo prostředku rozhraní API.

## <a name="azure-stack-request-uri-construct"></a>Azure Stack požadavku URI konstrukce

```
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version}
```

### <a name="uri-syntax"></a>Syntaxe identifikátoru URI

```
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version}
```

### <a name="query-uri-example"></a>Příklad dotazu identifikátoru URI

```
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>Další postup

Další informace o používání Azure RESTful koncové body, naleznete v tématu [Reference k rozhraní Azure REST API](https://docs.microsoft.com/rest/api/).
