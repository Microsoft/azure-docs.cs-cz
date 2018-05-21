---
title: Použití zásobníku rozhraní API v Azure | Microsoft Docs
description: Zjistěte, jak načíst ověření z Azure, aby se žádostí o rozhraní API k Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: e8a9489a3f487a45303bac45f805381b41427b4b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="use-the-azure-stack-api"></a>Použití zásobníku rozhraní API v Azure

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Azure zásobníku rozhraní API (Application Programming) můžete použít k automatizaci operací, jako je například syndicating položky marketplace.

Rozhraní API vyžaduje vašeho klienta k ověřování na základě koncového bodu Microsoft Azure přihlášení. Koncový bod vrátí token pro použití v hlavičce každý požadavek odeslaný rozhraní API služby Azure zásobníku. Microsoft Azure používá Oauth 2.0.

Tento článek obsahuje příklady, které používají **cURL** nástroj k vytváření žádostí o zásobník Azure. Aplikace, cURL, je nástroj příkazového řádku s knihovnou pro přenos dat. Tyto příklady provede proces získání tokenu pro přístup k rozhraní API služby Azure zásobníku. Většina programovacích jazyků poskytují knihovny Oauth 2.0, které mají robustní tokenu úlohy správy a popisovač tyto aktualizace tokenu.

Přečtěte si celý proces zásobníku REST API služby Azure pomocí obecné klienta REST, jako například **cURL**, které vám pomohou pochopit základní požadavky a uvádí, co můžete očekávat přijímat v datové části odpovědi.

V tomto článku není prozkoumejte všechny možnosti, které jsou k dispozici pro tokeny, jako je například interaktivní přihlášení načítání nebo vytváření vyhrazené ID aplikace. Chcete-li získat informace o těchto tématech, přečtěte si téma [referenční dokumentace rozhraní API REST Azure](https://docs.microsoft.com/rest/api/).

## <a name="get-a-token-from-azure"></a>Získání tokenu z Azure

Vytvořte obsah žádosti formátován pomocí typ obsahu x--www-form-urlencoded získat přístupový token. Odeslat požadavek na koncový bod ověřování REST Azure a přihlášení.

### <a name="uri"></a>URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**ID klienta** je buď:

 - Vaše doména klienta, jako například `fabrikam.onmicrosoft.com`
 - ID klienta, jako například `8eaed023-2b34-4da1-9baa-8bc8c9d6a491`
 - Výchozí hodnota pro klíče nezávislé na klienta: `common`

### <a name="post-body"></a>Textu POST

```bash  
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

Pro každou hodnotu:

 - **grant_type**  
    Typ schéma ověřování, které budete používat. V tomto příkladu je hodnota `password`

 - **Prostředek**  
    Přístup k prostředku token. Pomocí dotazu na koncový bod metadat správy zásobník Azure můžete najít prostředek. Podívejte se na **cílové skupiny** části

 - **Koncový bod správy Azure zásobníku**  
    ```
    https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
    ```

  > [!NOTE]  
  > Pokud se správce pokusí o přístup k rozhraní API klienta pak můžete musí nezapomeňte použít koncový bod klienta, například: `https://adminmanagement.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-011`  

  Například v zásobníku Azure Development Kit jako koncový bod:

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

  Tato hodnota je pevně zakódované na výchozí hodnotu:

  ```
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  Alternativní možnosti jsou dostupné pro konkrétní scénáře:

  
  | Aplikace | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

  **Uživatelské jméno**

  Například účtu AAD zásobník Azure:

  ```
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

  **Heslo**

  Heslo správce Azure zásobníku AAD.

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

Po získání přístupového tokenu, musíte ji přidat jako hlavičku pro každý z vašich žádosti o rozhraní API. Chcete-li tak učinit, je potřeba vytvořit hlavičku **autorizace** s hodnotou: `Bearer <access token>`. Příklad:

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

### <a name="url-structure-and-query-syntax"></a>Struktura a dotaz syntaxe adresy URL

Žádost o obecný identifikátor URI, se skládá z: {Schéma identifikátoru URI} :// {hostitel identifikátoru URI} / {cesta prostředku}? {řetězec dotazu}

- **Schéma identifikátoru URI**:  
Identifikátor URI označuje protokol použitý k odeslání žádosti. Například `http` nebo `https`.
- **Hostitel identifikátoru URI**:  
Hostitel Určuje název domény nebo IP adresa serveru pro koncový bod služby REST je hostitelem, jako například `graph.microsoft.com` nebo `adminmanagement.local.azurestack.external`.
- **Cesta prostředku**:  
Cesta Určuje prostředků nebo kolekce prostředků, které mohou obsahovat více segmenty, které používá služba při určování výběr tyto prostředky. Příklad: `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` lze použít k dotazování seznamu vlastníků konkrétní aplikace v rámci kolekce aplikací.
- **Řetězec dotazu**:  
Řetězec poskytuje dalších jednoduchý parametrů, třeba kritéria výběru verze nebo prostředek rozhraní API.

## <a name="azure-stack-request-uri-construct"></a>Azure konstrukce identifikátor URI požadavku zásobníku

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

Další informace o používání Azure RESTful koncových bodů najdete v tématu [referenční dokumentace rozhraní API REST Azure](https://docs.microsoft.com/rest/api/).
