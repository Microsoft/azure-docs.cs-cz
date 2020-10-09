---
title: Příklady transformací deklarací řetězcové deklarace pro vlastní zásady
titleSuffix: Azure AD B2C
description: Příklady transformace deklarací řetězcového pro schéma IEF (identity Experience Framework) Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1ad9cc3d6d07c8d744ec667e2fffb035848121b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85203244"
---
# <a name="stringcollection-claims-transformations"></a>Transformace deklarací řetězcové deklarace

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek popisuje příklady použití transformací deklarace identity kolekce řetězců schématu rozhraní identity Experience v Azure Active Directory B2C (Azure AD B2C). Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Přidá deklaraci identity řetězce do nové deklarace identitycollection řetězcecollection jedinečných hodnot.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | položka | řetězec | Deklarace ClaimType, která se má přidat do výstupní deklarace |
| InputClaim |  – kolekce | Třída StringCollection | Volitelné Je-li tento parametr zadán, transformace deklarací zkopíruje položky z této kolekce a přidá položku na konec deklarace výstupní kolekce. |
| OutputClaim |  – kolekce | Třída StringCollection | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací, s hodnotou zadanou ve vstupní deklaraci identity. |

Tuto transformaci deklarací použijte k přidání řetězce do nové nebo existující řetězcové. Běžně se používá v technickém profilu **AAD-UserWriteUsingAlternativeSecurityId** . Před vytvořením nového účtu sociální sítě **CreateOtherMailsFromEmail** transformuje deklarace ClaimType a přidá hodnotu do **otherMails** ClaimType.

Následující transformace deklarací identity přidá do **otherMails** ClaimType **e-mail** ClaimType.

```xml
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

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
  - **kolekce**: [" someone@outlook.com "]
  - **položka**: " admin@contoso.com "
- Deklarace výstupů:
  - **kolekce**: [" someone@outlook.com ", " admin@contoso.com "]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Přidá řetězcový parametr do nové deklarace identitycollection řetězcecollection jedinečných hodnot.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim |  – kolekce | Třída StringCollection | Volitelné Je-li tento parametr zadán, transformace deklarací zkopíruje položky z této kolekce a přidá položku na konec deklarace výstupní kolekce. |
| InputParameter | položka | řetězec | Hodnota, která se má přidat do výstupní deklarace |
| OutputClaim |  – kolekce | Třída StringCollection | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací, s hodnotou zadanou ve vstupním parametru. |

Tuto transformaci deklarací použijte k přidání řetězcové hodnoty do nové nebo existující řetězcové. Následující příklad přidá konstantu e-mailové adresy ( admin@contoso.com ) do **otherMails** deklarace identity.

```xml
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

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
  - **kolekce**: [" someone@outlook.com "]
- Vstupní parametry
  - **položka**: " admin@contoso.com "
- Deklarace výstupů:
  - **kolekce**: [" someone@outlook.com ", " admin@contoso.com "]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Získá první položku ze zadané kolekce řetězců.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim |  – kolekce | Třída StringCollection | ClaimTypes, které jsou používány transformací deklarací k získání položky. |
| OutputClaim | extractedItem | řetězec | ClaimTypes, které jsou vytvářeny po vyvolání tohoto ClaimsTransformation. První položka v kolekci |

Následující příklad přečte deklaraci identity **otherMails** a vrátí první položku do deklarace **e-mailu** .

```xml
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
  - **kolekce**: [" someone@outlook.com ", " someone@contoso.com "]
- Deklarace výstupů:
  - **extractedItem**: " someone@outlook.com "


## <a name="stringcollectioncontains"></a>StringCollectionContains

Zkontroluje, jestli typ deklarace identitycollection typu StringCollection obsahuje element.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | Třída StringCollection | Typ deklarace, který má být prohledán. |
|InputParameter|položka|řetězec|Hodnota, která se má hledat|
|InputParameter|ignoreCase|řetězec|Určuje, zda toto porovnání má ignorovat případ porovnávaných řetězců.|
| OutputClaim | outputClaim | boolean | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformation. Logický indikátor, pokud kolekce obsahuje takový řetězec |

Následující příklad ověří, zda `roles` typ deklarace StringCollection obsahuje hodnotu **admin**.

```xml
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

- Vstupní deklarace identity:
    - **inputClaim**: ["čtenář"; "Author"; "admin"]
- Vstupní parametry:
    - **položka**: "admin"
    - **IgnoreCase**: "pravda"
- Deklarace výstupů:
    - **outputClaim**: "true"

## <a name="stringcollectioncontainsclaim"></a>StringCollectionContainsClaim

Zkontroluje, jestli typ deklarace identitycollection typu StringCollection obsahuje hodnotu deklarace identity.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim |  – kolekce | Třída StringCollection | Typ deklarace, který má být prohledán. |
| InputClaim | položka|řetězec| Typ deklarace identity, který obsahuje hledanou hodnotu.|
|InputParameter|ignoreCase|řetězec|Určuje, zda toto porovnání má ignorovat případ porovnávaných řetězců.|
| OutputClaim | outputClaim | boolean | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformation. Logický indikátor, pokud kolekce obsahuje takový řetězec |

Následující příklad ověří, zda `roles` typ deklarace StringCollection obsahuje hodnotu `role` typu deklarace identity.

```xml
<ClaimsTransformation Id="HasRequiredRole" TransformationMethod="StringCollectionContainsClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="collection" />
    <InputClaim ClaimTypeReferenceId="role" TransformationClaimType="item" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hasAccess" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation> 
```

- Vstupní deklarace identity:
    - **kolekce**: ["čtenář"; "Author"; "admin"]
    - **položka**: "admin"
- Vstupní parametry:
    - **IgnoreCase**: "pravda"
- Deklarace výstupů:
    - **outputClaim**: "true"
