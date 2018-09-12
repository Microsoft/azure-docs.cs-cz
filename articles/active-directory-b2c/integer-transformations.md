---
title: Celé číslo deklaraci příklady transformaci identita prostředí Framework schéma z Azure Active Directory B2C | Dokumentace Microsoftu
description: Celé číslo deklaraci příklady transformaci identita prostředí Framework schéma z Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 988e25b6a5ef3f99ae7df9076a40e06b403bb029
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381202"
---
# <a name="integer-claims-transformations"></a>Celé číslo deklarace identity transformace

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek obsahuje příklady použití transformace deklarací celé číslo schématu architekturu rozhraní identit v Azure Active Directory (Azure AD) B2C. Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim 

Long – datový typ se převede na datový typ string.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | zem. šířka | Typ ClaimType převést na řetězec. |
| outputClaim | outputClaim | řetězec | Typ ClaimType, který je vytvořen po zavolání této ClaimsTransformation. |

V tomto příkladu `numericUserId` deklarace identity s typem hodnoty dlouho je převedena na `UserId` deklarace identity s typem hodnoty řetězce.

```XML
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **inputClaim**: 12334 (dlouhé)
- Výstupní deklarace identit: 
    - **outputClaim**: "12334" (string)

