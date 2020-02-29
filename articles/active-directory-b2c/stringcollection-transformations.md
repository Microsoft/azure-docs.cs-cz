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
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6aea537ebff4ae61e00861e6cafe742a7feb165e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186773"
---
# <a name="stringcollection-claims-transformations"></a>Transformace deklarací řetězcové deklarace

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek popisuje příklady použití transformací deklarace identity kolekce řetězců schématu rozhraní identity Experience v Azure Active Directory B2C (Azure AD B2C). Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Přidá deklaraci identity řetězce do nové deklarace identitycollection řetězcecollection jedinečných hodnot.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | položkami | řetězec | Deklarace ClaimType, která se má přidat do výstupní deklarace |
| inputClaim | kolekce | stringCollection | Volitelné Je-li tento parametr zadán, transformace deklarací zkopíruje položky z této kolekce a přidá položku na konec deklarace výstupní kolekce. |
| outputClaim | kolekce | stringCollection | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací, s hodnotou zadanou ve vstupní deklaraci identity. |

Tuto transformaci deklarací použijte k přidání řetězce do nové nebo existující řetězcové. Běžně se používá v technickém profilu **AAD-UserWriteUsingAlternativeSecurityId** . Před vytvořením nového účtu sociální sítě **CreateOtherMailsFromEmail** transformuje deklarace ClaimType a přidá hodnotu do **otherMails** ClaimType.

Následující transformace deklarací identity přidá do **otherMails** ClaimType **e-mail** ClaimType.

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

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
  - **kolekce**: ["someone@outlook.com"]
  - **položka**: "admin@contoso.com"
- Deklarace výstupů:
  - **kolekce**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Přidá řetězcový parametr do nové deklarace identitycollection řetězcecollection jedinečných hodnot.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | kolekce | stringCollection | Volitelné Je-li tento parametr zadán, transformace deklarací zkopíruje položky z této kolekce a přidá položku na konec deklarace výstupní kolekce. |
| InputParameter | položkami | řetězec | Hodnota, která se má přidat do výstupní deklarace |
| outputClaim | kolekce | stringCollection | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací, s hodnotou zadanou ve vstupním parametru. |

Tuto transformaci deklarací použijte k přidání řetězcové hodnoty do nové nebo existující řetězcové. V následujícím příkladu je do **otherMails** deklarace identity přidána konstantní e-mailová adresa (admin@contoso.com).

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

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
  - **kolekce**: ["someone@outlook.com"]
- Vstupní parametry
  - **položka**: "admin@contoso.com"
- Deklarace výstupů:
  - **kolekce**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Získá první položku ze zadané kolekce řetězců.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | kolekce | stringCollection | ClaimTypes, které jsou používány transformací deklarací k získání položky. |
| outputClaim | extractedItem | řetězec | ClaimTypes, které jsou vytvářeny po vyvolání tohoto ClaimsTransformation. První položka v kolekci |

Následující příklad přečte deklaraci identity **otherMails** a vrátí první položku do deklarace **e-mailu** .

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

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
  - **kolekce**: ["someone@outlook.com", "someone@contoso.com"]
- Deklarace výstupů:
  - **extractedItem**: "someone@outlook.com"


## <a name="stringcollectioncontains"></a>StringCollectionContains

Zkontroluje, jestli typ deklarace identitycollection typu StringCollection obsahuje element.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | stringCollection | Typ deklarace, který má být prohledán. |
|InputParameter|položkami|řetězec|Hodnota, která se má hledat|
|InputParameter|ignoreCase|řetězec|Určuje, zda toto porovnání má ignorovat případ porovnávaných řetězců.|
| outputClaim | outputClaim | Boolean | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformation. Logický indikátor, pokud kolekce obsahuje takový řetězec |

Následující příklad zkontroluje, zda typ deklarace `roles` StringCollection obsahuje hodnotu **admin**.

```XML
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


