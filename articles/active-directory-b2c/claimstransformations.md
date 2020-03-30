---
title: Transformace deklarací – služba Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Definice claimsTransformations prvek v identity experience framework schéma služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e71d521dce40f6a8ec81286fcc95dc97bf10078c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189732"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**ClaimsTransformations** Element obsahuje seznam funkcí transformace deklarací, které lze použít v cestách uživatelů jako součást [vlastní zásady](custom-policy-overview.md). Transformace deklarací převede danou deklaraci na jinou. V transformaci deklarací určujete metodu transformace, například přidání položky do kolekce řetězců nebo změnu případu řetězce.

Chcete-li zahrnout seznam funkcí transformace deklarací, které lze použít v cestách uživatele, musí být element XML ClaimsTransformations deklarován v části BuildingBlocks zásady.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

**ClaimsTransformation** Element obsahuje následující atributy:

| Atribut |Požaduje se | Popis |
| --------- |-------- | ----------- |
| ID |Ano | Identifikátor, který se používá k jednoznačné identifikaci transformace deklarace identity. Identifikátor je odkazován z jiných prvků XML v zásadě. |
| Metoda transformace | Ano | Metoda transformace, která má být používána v transformaci deklarací identity. Každá transformace deklarace má své vlastní hodnoty. Úplný seznam dostupných hodnot naleznete v [odkazu na transformaci deklarací identity.](#claims-transformations-reference) |

## <a name="claimstransformation"></a>ClaimsTransformation

**ClaimsTransformation** Prvek obsahuje následující prvky:

```xml
<ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
  <InputClaims>
    ...
  </InputClaims>
  <InputParameters>
    ...
  </InputParameters>
  <OutputClaims>
    ...
  </OutputClaims>
</ClaimsTransformation>
```


| Element | Výskyty | Popis |
| ------- | -------- | ----------- |
| Vstupní deklarace | 0:1 | Seznam **InputClaim** prvky, které určují typy deklarací identity, které jsou brány jako vstup pro transformaci deklarací identity. Každý z těchto prvků obsahuje odkaz na ClaimType již definována v ClaimsSchema části v zásadách. |
| Inputparameters | 0:1 | Seznam **InputParameter** prvky, které jsou k dispozici jako vstup pro transformaci deklarací identity.
| OutputClaims | 0:1 | Seznam **OutputClaim** prvky, které určují typy deklarací, které jsou vytvořeny po ClaimsTransformation byla vyvolána. Každý z těchto prvků obsahuje odkaz na ClaimType již definována v ClaimsSchema části. |

### <a name="inputclaims"></a>Vstupní deklarace

**InputClaims** Element obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Vstupní deklarace | 1:n | Očekávaný vstupní typ deklarace. |

#### <a name="inputclaim"></a>Vstupní deklarace

**InputClaim** Element obsahuje následující atributy:

| Atribut |Požaduje se | Popis |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |Ano | Odkaz na ClaimType již definována v claimsSchema části v zásadě. |
| TransformationClaimType |Ano | Identifikátor odkazující na typ deklarace transformace. Každá transformace deklarace má své vlastní hodnoty. Úplný seznam dostupných hodnot naleznete v [odkazu na transformaci deklarací identity.](#claims-transformations-reference) |

### <a name="inputparameters"></a>Inputparameters

Prvek **InputParameters** obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Parametr Input | 1:n | Očekávaný vstupní parametr. |

#### <a name="inputparameter"></a>Parametr Input

| Atribut | Požaduje se |Popis |
| --------- | ----------- |----------- |
| ID | Ano | Identifikátor, který je odkazna parametr metody transformace deklarací. Každá metoda transformace deklarací má své vlastní hodnoty. Úplný seznam dostupných hodnot naleznete v tabulce transformace deklarací. |
| DataType | Ano | Typ dat parametru, například String, Boolean, Int nebo DateTime podle výčtu DataType ve schématu XML vlastní zásady. Tento typ se používá ke správnému provádění aritmetických operací. Každá transformace deklarace má své vlastní hodnoty. Úplný seznam dostupných hodnot naleznete v [odkazu na transformaci deklarací identity.](#claims-transformations-reference) |
| Hodnota | Ano | Hodnota, která je předána doslovně transformace. Některé hodnoty jsou libovolné, některé z nich vyberete z metody transformace deklarací. |

### <a name="outputclaims"></a>OutputClaims

**OutputClaims** Element obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Výstupní nárok | 0:n | Očekávaný typ deklarace výstupu. |

#### <a name="outputclaim"></a>Výstupní nárok

Element **OutputClaim** obsahuje následující atributy:

| Atribut |Požaduje se | Popis |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | Ano | Odkaz na ClaimType již definována v claimsSchema části v zásadě.
| TransformationClaimType | Ano | Identifikátor odkazující na typ deklarace transformace. Každá transformace deklarace má své vlastní hodnoty. Úplný seznam dostupných hodnot naleznete v [odkazu na transformaci deklarací identity.](#claims-transformations-reference) |

Pokud vstupní deklarace a výstupní deklarace jsou stejného typu (řetězec nebo logická hodnota), můžete použít stejnou vstupní deklaraci jako výstupní deklarace. V tomto případě transformace deklarací změní vstupní deklaraci s výstupní hodnotou.

## <a name="example"></a>Příklad

Můžete například uložit poslední verzi smluvních podmínek, které uživatel přijal. Při aktualizaci podmínek služeb můžete požádat uživatele o přijetí nové verze. V následujícím příkladu **hastosversionchanged** deklarace transformace porovná hodnotu **deklarace TOSVersion** s hodnotou **LastTOSAcceptedVersion** deklarace a potom vrátí logickou **deklaraci TOSVersionChanged.**

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="TOSVersionChanged">
      <DisplayName>Indicates if the TOS version accepted by the end user is equal to the current version</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="TOSVersion">
      <DisplayName>TOS version</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="LastTOSAcceptedVersion">
      <DisplayName>TOS version accepted by the end user</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <ClaimsTransformation Id="HasTOSVersionChanged" TransformationMethod="CompareClaims">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="TOSVersion" TransformationClaimType="inputClaim1" />
        <InputClaim ClaimTypeReferenceId="LastTOSAcceptedVersion" TransformationClaimType="inputClaim2" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="TOSVersionChanged" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

## <a name="claims-transformations-reference"></a>Odkaz na transformace deklarací identity

Příklady transformací deklarací naleznete na následujících odkazových stránkách:

- [Logická hodnota](boolean-transformations.md)
- [Datum](date-transformations.md)
- [Celé číslo](integer-transformations.md)
- [JSON](json-transformations.md)
- [Obecné](general-transformations.md)
- [Sociální účet](social-transformations.md)
- [Řetězec](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

