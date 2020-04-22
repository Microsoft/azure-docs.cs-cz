---
title: StringCollection deklarace transformace příklady pro vlastní zásady
titleSuffix: Azure AD B2C
description: StringCollection nároky příklady transformace pro rozhraní Identity Experience Framework (IEF) schéma Služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cac7e6feb632456b63b97ead057f9ecaf49322ea
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729719"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection deklarace identity transformace

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek obsahuje příklady pro použití kolekce řetězců deklarace identity schématu rozhraní identity v Azure Active Directory B2C (Azure AD B2C). Další informace naleznete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Přidá deklaraci řetězce do deklarace nové jedinečné hodnoty stringCollection.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | položka | řetězec | ClaimType, které mají být přidány do výstupní deklarace. |
| Vstupní deklarace |  – kolekce | Stringcollection | [Nepovinné] Pokud je zadán, deklarace transformace zkopíruje položky z této kolekce a přidá položku na konec deklarace výstupní kolekce. |
| Výstupní nárok |  – kolekce | Stringcollection | ClaimType, který je vytvořen po této deklarace transformace byla vyvolána, s hodnotou zadanou ve vstupní deklaraci. |

Pomocí této transformace deklarace identity přidat řetězec do nové nebo existující stringCollection. Běžně se používá v **technickém profilu AAD-UserWriteUsingAlternativeSecurityId.** Před vytvořením nového účtu sociální sítě **CreateOtherMailsFromEmail** deklarace identity transformace přečte ClaimType a přidá hodnotu **otherMails** ClaimType.

Následující deklarace transformace přidá **e-mail** ClaimType **otherMails** ClaimType.

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

- Vstupní deklarace:
  - **kolekce**:someone@outlook.com[" "]
  - **položka**:admin@contoso.com" "
- Výstupní nároky:
  - **kolekce**:someone@outlook.com["admin@contoso.com", " "]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Přidá parametr řetězce do nové deklarace vlastností stringCollection jedinečných hodnot.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace |  – kolekce | Stringcollection | [Nepovinné] Pokud je zadán, deklarace transformace zkopíruje položky z této kolekce a přidá položku na konec deklarace výstupní kolekce. |
| Parametr Input | položka | řetězec | Hodnota, která má být přidána do výstupní deklarace. |
| Výstupní nárok |  – kolekce | Stringcollection | ClaimType, který je vytvořen po této deklarace transformace byla vyvolána, s hodnotou zadanou ve vstupním parametru. |

Pomocí této transformace deklarace identity přidat hodnotu řetězce do nové nebo existující stringCollection. Následující příklad přidá konstantní e-mailovou adresu (admin@contoso.com) k jiné **deklaraci Pošta.**

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

- Vstupní deklarace:
  - **kolekce**:someone@outlook.com[" "]
- Vstupní parametry
  - **položka**:admin@contoso.com" "
- Výstupní nároky:
  - **kolekce**:someone@outlook.com["admin@contoso.com", " "]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Získá první položku z kolekce zadaný řetězec.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace |  – kolekce | Stringcollection | ClaimTypes, které jsou používány transformace deklarací získat položku. |
| Výstupní nárok | extrahovaná položka | řetězec | ClaimTypes, které jsou vytvořeny po této ClaimsTransformation byla vyvolána. První položka v kolekci. |

Následující příklad přečte **otherMails** deklarace a vrátí první položku do **e-mailové** deklarace.

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

- Vstupní deklarace:
  - **kolekce**:someone@outlook.com["someone@contoso.com", " "]
- Výstupní nároky:
  - **extrahovanéPoložky**: "someone@outlook.com"


## <a name="stringcollectioncontains"></a>Kolekce stringObsahuje

Zkontroluje, zda typ deklarace StringCollection obsahuje prvek.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputClaim | Stringcollection | Typ deklarace, který má být prohledán. |
|Parametr Input|položka|řetězec|Hodnota hledání.|
|Parametr Input|Ignorecase|řetězec|Určuje, zda má toto porovnání ignorovat případ porovnávaných řetězců.|
| Výstupní nárok | outputClaim | Boolean | ClaimType, který je vytvořen po této ClaimsTransformation byla vyvolána. Logický indikátor, pokud kolekce obsahuje takový řetězec |

Následující příklad zkontroluje, `roles` zda typ deklarace stringCollection obsahuje hodnotu **admin**.

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

- Vstupní deklarace:
    - **inputClaim**: ["čtenář", "autor", "admin"]
- Vstupní parametry:
    - **položka**: "Admin"
    - **ignoreCase**: "true"
- Výstupní nároky:
    - **outputClaim**: "true"

## <a name="stringcollectioncontainsclaim"></a>Deklarace stringCollectionContainsClaim

Zkontroluje, zda typ deklarace StringCollection obsahuje hodnotu deklarace.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace |  – kolekce | Stringcollection | Typ deklarace, který má být prohledán. |
| Vstupní deklarace | položka|řetězec| Typ deklarace, který obsahuje hodnotu pro vyhledávání.|
|Parametr Input|Ignorecase|řetězec|Určuje, zda má toto porovnání ignorovat případ porovnávaných řetězců.|
| Výstupní nárok | outputClaim | Boolean | ClaimType, který je vytvořen po této ClaimsTransformation byla vyvolána. Logický indikátor, pokud kolekce obsahuje takový řetězec |

Následující příklad zkontroluje, `roles` zda typ deklarace `role` stringCollection obsahuje hodnotu typu deklarace.

```XML
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

- Vstupní deklarace:
    - **kolekce**: ["čtenář", "autor", "admin"]
    - **položka**: "Admin"
- Vstupní parametry:
    - **ignoreCase**: "true"
- Výstupní nároky:
    - **outputClaim**: "true"
