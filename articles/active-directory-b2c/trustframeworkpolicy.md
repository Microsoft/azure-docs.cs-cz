---
title: TrustFrameworkPolicy-Azure Active Directory B2C | Microsoft Docs
description: Zadejte element TrustFrameworkPolicy vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9bf1cc197a7d6977ccb6ef69e157d9f8a76a58d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103470729"
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
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


Element **TrustFrameworkPolicy** obsahuje následující atributy:

| Atribut | Povinné | Popis |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Yes | Verze schématu, která se má použít ke spuštění zásad. Hodnota musí být `0.3.0.0` |
| TenantObjectId | No | Jedinečný identifikátor objektu klienta Azure Active Directory B2C (Azure AD B2C). |
| TenantId | Yes | Jedinečný identifikátor tenanta, ke kterému patří tato zásada |
| PolicyId | Yes | Jedinečný identifikátor pro zásady Tento identifikátor musí být předponou *B2C_1A_* |
| PublicPolicyUri | Yes | Identifikátor URI pro zásadu, která je kombinací ID tenanta a ID zásad. |
| DeploymentMode | No | Možné hodnoty: `Production` , nebo `Development` . `Production` je výchozí možnost. Tato vlastnost slouží k ladění zásad. Další informace najdete v tématu [shromažďování protokolů](troubleshoot-with-application-insights.md). |
| UserJourneyRecorderEndpoint | No | Koncový bod, který se používá k protokolování. Hodnota musí být nastavena na hodnotu, `urn:journeyrecorder:applicationinsights` Pokud atribut existuje. Další informace najdete v tématu [shromažďování protokolů](troubleshoot-with-application-insights.md). |


Následující příklad ukazuje, jak zadat element **TrustFrameworkPolicy** :

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

Element **TrustFrameworkPolicy** obsahuje následující prvky:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| BasePolicy| 0:1| Identifikátor základní zásady. |
| [BuildingBlocks](buildingblocks.md) | 0:1 | Stavební bloky vašich zásad. |
| [ClaimsProviders](claimsproviders.md) | 0:1 | Kolekce zprostředkovatelů deklarací. |
| [UserJourneys](userjourneys.md) | 0:1 | Kolekce uživatelských cest. |
| [RelyingParty](relyingparty.md) | 0:1 | Definice zásady předávající strany. |

Aby bylo možné dědit zásadu z jiné zásady, musí být deklarován element **BasePolicy** v rámci **TrustFrameworkPolicy** elementu souboru zásad. Element **BasePolicy** je odkaz na základní zásady, ze kterých je tato zásada odvozena.

Element **BasePolicy** obsahuje následující prvky:

| Prvek | Výskytů | Description |
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
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

