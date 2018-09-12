---
title: Třída StringCollection deklaraci příklady transformaci identita prostředí Framework schéma z Azure Active Directory B2C | Dokumentace Microsoftu
description: Třída StringCollection deklaraci příklady transformaci identita prostředí Framework schéma z Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: cfc190d862c161783c2dd5fc7f03b7bb1ae6bed9
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381123"
---
# <a name="stringcollection-claims-transformations"></a>Třída StringCollection deklarace identity transformace

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek obsahuje příklady použití transformace deklarací identity kolekce řetězců schématu architekturu rozhraní identit v Azure Active Directory (Azure AD) B2C. Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Přidá řetězec deklarace identity do nové třída stringCollection deklarace. 

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Položka | řetězec | Přidat do výstupu deklarace typu deklarace identity. |
| InputClaim | kolekce | Třída stringCollection | [Volitelné] -Li zadána, transformace deklarací identity zkopíruje položky z této kolekce a přidá položku do konce výstupní kolekce deklarací. |
| outputClaim | kolekce | Třída stringCollection | ClaimTypes vytvořený po zavolání této ClaimsTransformation. |

Použijte Tato transformace přidat řetězec nové nebo existující třída stringCollection deklarací identity. Se běžně používá **AAD UserWriteUsingAlternativeSecurityId** technický profil. Před vytvořením nového účtu na sociální síti **CreateOtherMailsFromEmail** transformaci deklarací identity načte typ ClaimType a přidá hodnotu **otherMails** typu deklarace identity. 

Přidá následující transformace deklarací identity **e-mailu** typu deklarace identity k **otherMails** typu deklarace identity.

```XML
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **kolekce**: ["someone@outlook.com"]
    - **Položka**: "admin@contoso.com"
- Výstupní deklarace identit: admin@contoso.com
    - **kolekce**: ["someone@outlook.com","admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Přidá novou deklaraci třída stringCollection parametr řetězce. 

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | kolekce | Třída stringCollection | [Volitelné] -Li zadána, transformace deklarací identity zkopíruje položky z této kolekce a přidá položku do konce výstupní kolekce deklarací. |
| InputParameter | Položka | řetězec | Hodnota přidávaného do výstupní deklarací. |
| outputClaim | kolekce | Třída stringCollection | ClaimTypes, který bude vytvořen po zavolání této ClaimsTransformation. |

Použijte Tato transformace přidat hodnotu řetězce nové nebo existující třída stringCollection deklarací identity. Následující příklad přidá konstanty e-mailovou adresu (admin@contoso.com) k **otherMails** deklarací identity. 

```XML
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **kolekce**: ["someone@outlook.com"]
- Vstupní parametry 
    - **Položka**: "admin@contoso.com"
- Výstupní deklarace identit: admin@contoso.com
    - **kolekce**: ["someone@outlook.com","admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Získá první položku z kolekce zadaný řetězec. 

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | kolekce | Třída stringCollection | ClaimTypes, které jsou používány transformace deklarací identity k získání položky. |
| outputClaim | extractedItem | řetězec | ClaimTypes vytvořený po zavolání této ClaimsTransformation. První položka v kolekci. |

Následující příklad načte **otherMails** deklarace identity a vrátí první položku do **e-mailu** deklarací identity. 

```XML
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **kolekce**: ["someone@outlook.com","someone@contoso.com"]
- Výstupní deklarace identit: 
    - **extractedItem**: "someone@outlook.com"

