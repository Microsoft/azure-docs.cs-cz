---
title: Příklady transformací celočíselných deklarací pro vlastní zásady
titleSuffix: Azure AD B2C
description: Příklady transformace celočíselných deklarací pro schéma IEF (identity Experience Framework) Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ece25e95bbbe9f1a1fa591c29ea9ffda0fefd369
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78187674"
---
# <a name="integer-claims-transformations"></a>Transformace celočíselných deklarací

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek popisuje příklady použití celočíselných transformací identity schématu rozhraní identity Experience v Azure Active Directory B2C (Azure AD B2C). Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Převede datový typ Long na datový typ String.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | Deklarace ClaimType pro převod na řetězec. |
| OutputClaim | outputClaim | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformation. |

V tomto příkladu je `numericUserId` deklarace identity s typem hodnoty Long převedena na `UserId` deklaraci s typem hodnoty String.

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

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **inputClaim**: 12334 (dlouhý)
- Deklarace výstupů:
    - **outputClaim**: "12334" (řetězec)

