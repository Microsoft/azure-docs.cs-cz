---
title: ClaimsTransformations-Azure Active Directory B2C | Microsoft Docs
description: Definice prvku ClaimsTransformations ve schématu rozhraní identity Experience Framework Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37d9bd78a80ac52d2a790537bf47e33807720349
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85202955"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **ClaimsTransformations** obsahuje seznam transformačních funkcí deklarací identity, které se dají použít v rámci cest uživatelů jako součást [vlastních zásad](custom-policy-overview.md). Transformace deklarací identity převede danou deklaraci identity na jinou. V transformaci deklarací identity zadáte metodu transformace, například přidání položky do kolekce řetězců nebo změna velikosti písmen řetězce.

Chcete-li zahrnout seznam transformačních funkcí deklarací identity, které lze použít v rámci cest uživatelů, musí být element XML ClaimsTransformations deklarován v části BuildingBlocks v zásadách.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

Element **ClaimsTransformation** obsahuje následující atributy:

| Atribut |Povinné | Popis |
| --------- |-------- | ----------- |
| Id |Ano | Identifikátor, který slouží k jednoznačné identifikaci transformace deklarace identity. Identifikátor je odkazován z dalších prvků XML v zásadě. |
| TransformationMethod | Ano | Metoda transformace, která se má použít v transformaci deklarací Každá transformace deklarace má své vlastní hodnoty. Úplný seznam dostupných hodnot najdete v [referenčních informacích o transformaci deklarací identity](#claims-transformations-reference) . |

## <a name="claimstransformation"></a>ClaimsTransformation

Element **ClaimsTransformation** obsahuje následující prvky:

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


| Prvek | Výskytů | Popis |
| ------- | -------- | ----------- |
| InputClaims | 0:1 | Seznam **InputClaim** prvků, které určují typy deklarací identity, které jsou pořízeny jako vstup pro transformaci deklarací. Každý z těchto prvků obsahuje odkaz na objekt ClaimType, který je již definován v části ClaimsSchema v zásadách. |
| Parametry | 0:1 | Seznam **InputParameter** prvků, které jsou zadány jako vstup pro transformaci deklarací.
| OutputClaims | 0:1 | Seznam elementů **OutputClaim** , které určují typy deklarací, které jsou vytvářeny po vyvolání ClaimsTransformation. Každý z těchto elementů obsahuje odkaz na objekt ClaimType, který je již definován v oddílu ClaimsSchema. |

### <a name="inputclaims"></a>InputClaims

Element **InputClaims** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| InputClaim | 1: n | Očekával se vstupní typ deklarace identity. |

#### <a name="inputclaim"></a>InputClaim

Element **InputClaim** obsahuje následující atributy:

| Atribut |Povinné | Popis |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |Ano | Odkaz na objekt ClaimType již definovaný v části ClaimsSchema v zásadách. |
| TransformationClaimType |Ano | Identifikátor, na který se má odkazovat typ deklarace transformace Každá transformace deklarace má své vlastní hodnoty. Úplný seznam dostupných hodnot najdete v [referenčních informacích o transformaci deklarací identity](#claims-transformations-reference) . |

### <a name="inputparameters"></a>Parametry

Element **vstupní parametry** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| InputParameter | 1: n | Očekával se vstupní parametr. |

#### <a name="inputparameter"></a>InputParameter

| Atribut | Povinné |Popis |
| --------- | ----------- |----------- |
| Id | Ano | Identifikátor, který je odkazem na parametr metody transformace deklarací identity. Každá metoda transformace deklarací má své vlastní hodnoty. Úplný seznam dostupných hodnot najdete v tabulce pro transformaci deklarací identity. |
| DataType | Ano | Typ dat parametru, jako je například String, Boolean, int nebo DateTime, podle výčtu DataType ve schématu XML vlastní zásady. Tento typ se používá k správnému provádění aritmetických operací. Každá transformace deklarace má své vlastní hodnoty. Úplný seznam dostupných hodnot najdete v [referenčních informacích o transformaci deklarací identity](#claims-transformations-reference) . |
| Hodnota | Ano | Hodnota, která je předána do transformace. Některé z těchto hodnot jsou libovolné, některé z nich si vybíráte z metody transformace deklarací identity. |

### <a name="outputclaims"></a>OutputClaims

Element **OutputClaims** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| OutputClaim | 0: n | Očekával se výstupní typ deklarace identity. |

#### <a name="outputclaim"></a>OutputClaim

Element **OutputClaim** obsahuje následující atributy:

| Atribut |Povinné | Popis |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | Ano | Odkaz na objekt ClaimType již definovaný v části ClaimsSchema v zásadách.
| TransformationClaimType | Ano | Identifikátor, na který se má odkazovat typ deklarace transformace Každá transformace deklarace má své vlastní hodnoty. Úplný seznam dostupných hodnot najdete v [referenčních informacích o transformaci deklarací identity](#claims-transformations-reference) . |

Pokud vstupní deklarace identity a výstupní deklarace identity jsou stejného typu (řetězec nebo logická hodnota), můžete jako výstupní deklaraci identity použít stejnou vstupní deklaraci identity. V takovém případě transformace deklarací změní vstupní deklaraci na výstupní hodnotu.

## <a name="example"></a>Příklad

Můžete například uložit poslední verzi vašich podmínek služby, kterou uživatel přijal. Když aktualizujete podmínky služeb, můžete požádat uživatele, aby přijal novou verzi. V následujícím příkladu transformace deklarací **HasTOSVersionChanged** porovnává hodnotu deklarace identity **TOSVersion** s hodnotou deklarace **LastTOSAcceptedVersion** a potom vrátí logickou deklaraci identity **TOSVersionChanged** .

```xml
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

## <a name="claims-transformations-reference"></a>Odkazy na transformace deklarací identity

Příklady transformací deklarací identity najdete na následujících odkazových stránkách:

- [Logická hodnota](boolean-transformations.md)
- [Date](date-transformations.md) (Datum)
- [Integer](integer-transformations.md)
- [JSON](json-transformations.md)
- [Telefonní číslo](phone-number-claims-transformations.md)
- [Obecné](general-transformations.md)
- [Účet sociální sítě](social-transformations.md)
- [Řetězec](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

