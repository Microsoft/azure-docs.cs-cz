---
title: Celé číslo deklarace transformace příklady pro vlastní zásady
titleSuffix: Azure AD B2C
description: Celé číslo deklarace transformace příklady pro rozhraní Identity Experience Framework (IEF) schéma Služby Azure Active Directory B2C.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187674"
---
# <a name="integer-claims-transformations"></a>Celé číslo deklarace identity transformace

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek obsahuje příklady pro použití celé číslo nároky transformace schématu rozhraní identity rozhraní ve službě Azure Active Directory B2C (Azure AD B2C). Další informace naleznete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>Převéstdeklaraci řetězce

Převede dlouhý datový typ na datový typ řetězce.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputClaim | long | ClaimType převést na řetězec. |
| Výstupní nárok | outputClaim | řetězec | ClaimType, který je vytvořen po této ClaimsTransformation byla vyvolána. |

V tomto příkladu `numericUserId` je deklarace s typem `UserId` hodnoty long převedena na deklaraci s typem hodnoty řetězce.

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

- Vstupní deklarace:
    - **inputClaim**: 12334 (dlouhý)
- Výstupní nároky:
    - **outputClaim**: "12334" (řetězec)

