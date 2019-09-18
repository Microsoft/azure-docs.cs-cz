---
title: Příklady transformací celočíselných deklarací pro schéma rozhraní Azure Active Directory B2C pro prostředí identity | Microsoft Docs
description: Příklady transformací celočíselných deklarací pro schéma rozhraní Azure Active Directory B2C prostředí identity
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d82f7fecfb35c63d586993fed73a83209782a890
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064219"
---
# <a name="integer-claims-transformations"></a>Transformace celočíselných deklarací

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek popisuje příklady použití celočíselných transformací identity schématu rozhraní identity Experience v Azure Active Directory B2C (Azure AD B2C). Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Převede datový typ Long na datový typ String.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | long | Deklarace ClaimType pro převod na řetězec. |
| OutputClaim | OutputClaim | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformation. |

V tomto příkladu `numericUserId` je deklarace identity s typem hodnoty Long převedena `UserId` na deklaraci s typem hodnoty String.

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

