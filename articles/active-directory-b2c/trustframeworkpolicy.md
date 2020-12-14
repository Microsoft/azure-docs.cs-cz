---
title: TrustFrameworkPolicy-Azure Active Directory B2C | Microsoft Docs
description: Zadejte element TrustFrameworkPolicy vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 354c6f9710b7cbd70e0631bc973b2482ea8d8bb3
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97386880"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Vlastní zásady jsou reprezentovány jako jeden nebo více souborů ve formátu XML, které na sebe navzájem odkazují v hierarchickém řetězu. Prvky XML definují prvky zásad, například schéma deklarací identity, transformace deklarací identity, definice obsahu, zprostředkovatele deklarací, technické profily, cesty uživatelů a kroky orchestrace. Jednotlivé soubory zásad se definují v rámci souboru zásad na nejvyšší úrovni v **TrustFrameworkPolicy** elementu.

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


Element **TrustFrameworkPolicy** obsahuje následující atributy:

| Atribut | Povinné | Popis |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Ano | Verze schématu, která se má použít ke spuštění zásad. Hodnota musí být `0.3.0.0` |
| TenantObjectId | Ne | Jedinečný identifikátor objektu klienta Azure Active Directory B2C (Azure AD B2C). |
| TenantId | Ano | Jedinečný identifikátor tenanta, ke kterému patří tato zásada |
| PolicyId | Ano | Jedinečný identifikátor pro zásady Tento identifikátor musí být předponou *B2C_1A_* |
| PublicPolicyUri | Ano | Identifikátor URI pro zásadu, která je kombinací ID tenanta a ID zásad. |
| DeploymentMode | Ne | Možné hodnoty: `Production` , nebo `Development` . `Production` je výchozí možnost. Tato vlastnost slouží k ladění zásad. Další informace najdete v tématu [shromažďování protokolů](troubleshoot-with-application-insights.md). |
| UserJourneyRecorderEndpoint | Ne | Koncový bod, který se používá, pokud je **DeploymentMode** nastaveno na `Development` . Hodnota musí být `urn:journeyrecorder:applicationinsights` . Další informace najdete v tématu [shromažďování protokolů](troubleshoot-with-application-insights.md). |


Následující příklad ukazuje, jak zadat element **TrustFrameworkPolicy** :

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

Element **TrustFrameworkPolicy** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| BasePolicy| 0:1| Identifikátor základní zásady. |
| [BuildingBlocks](buildingblocks.md) | 0:1 | Stavební bloky vašich zásad. |
| [ClaimsProviders](claimsproviders.md) | 0:1 | Kolekce zprostředkovatelů deklarací. |
| [UserJourneys](userjourneys.md) | 0:1 | Kolekce uživatelských cest. |
| [RelyingParty](relyingparty.md) | 0:1 | Definice zásady předávající strany. |

Aby bylo možné dědit zásadu z jiné zásady, musí být deklarován element **BasePolicy** v rámci **TrustFrameworkPolicy** elementu souboru zásad. Element **BasePolicy** je odkaz na základní zásady, ze kterých je tato zásada odvozena.

Element **BasePolicy** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | --------|
| TenantId | 1:1 | Identifikátor vašeho tenanta Azure AD B2C. |
| PolicyId | 1:1 | Identifikátor nadřazené zásady |


Následující příklad ukazuje, jak zadat základní zásady. Tato zásada **B2C_1A_TrustFrameworkExtensions** se odvozuje od zásad **B2C_1A_TrustFrameworkBase** .

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

