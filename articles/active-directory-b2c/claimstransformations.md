---
title: ClaimsTransformations – Azure Active Directory B2C | Dokumentace Microsoftu
description: Definice elementu ClaimsTransformations v Identity prostředí Framework schéma z Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7e4f3d4cd66760fd2cca799e929d2035c0ba3fd0
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381205"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**ClaimsTransformations** prvek obsahuje seznam funkcí transformace deklarací identity, které je možné v centrech uživatele jako součást [vlastní zásady](active-directory-b2c-overview-custom.md). Transformace deklarací identity převede daný deklarace identity na jiný. V transformaci deklarací identity zadejte metodu transformace, třeba přidání položky do kolekce řetězců nebo změna velikosti písmen řetězce.

Seznam funkcí transformace deklarací identity, které je možné zahrnout cesty uživatele, musí být deklarován element ClaimsTransformations XML části BuildingBlocks zásad.

```xml
<ClaimsTransformations Id="<identifier>" TransformationMethod="<method>">
  ...
</ClaimsTransformation>
```

**ClaimsTransformations** element conatains následující atributy:

| Atribut |Požaduje se | Popis |
| --------- |-------- | ----------- |
| ID |Ano | Identifikátor, který slouží k jednoznačné identifikaci transformace deklarací identity. Identifikátor je odkazováno z dalších prvky XML v zásadách. |
| TransformationMethod | Ano | Metoda transformace používané k transformaci deklarací identity. Každá transformace deklarací identity má své vlastní hodnoty. Zobrazit [odkaz na transformaci deklarací](#Claims-transformations-reference) úplný seznam dostupných hodnot. |

## <a name="claimstransformation"></a>ClaimsTransformation

**ClaimsTransformation** prvek obsahuje následující prvky:

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
| InputClaims | 0:1 | Seznam **InputClaim** elementy, které určují typy deklarací, které jsou použity jako vstup do transformace deklarací identity. Každý z těchto elementů obsahuje odkaz na typu deklarace identity, již definovaná v části ClaimsSchema v zásadách. |
| Vstupní parametry | 0:1 | Seznam **InputParameter** prvky, které jsou k dispozici jako vstup pro transformaci deklarací identity.  
| OutputClaims | 0:1 | Seznam **OutputClaim** elementy, které určují typy deklarací identity, které jsou vytvořené po zavolání ClaimsTransformation. Každý z těchto elementů obsahuje odkaz na typu deklarace identity, již definovaná v části ClaimsSchema. |

### <a name="inputclaims"></a>InputClaims

**InputClaims** prvek obsahuje následující element:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| InputClaim | 1: n | Typ deklarace identity očekávaný vstup. |

#### <a name="inputclaim"></a>InputClaim

**InputClaim** prvek obsahuje následující atributy:

| Atribut |Požaduje se | Popis |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |Ano | Odkaz na typu deklarace identity, již definovaná v části ClaimsSchema v zásadách. |
| TransformationClaimType |Ano | Typ deklarace identifikátoru tak, aby odkazovaly transformaci. Každá transformace deklarací identity má své vlastní hodnoty. Zobrazit [odkaz na transformaci deklarací](#Claims-transformations-reference) úplný seznam dostupných hodnot. |

### <a name="inputparameters"></a>Vstupní parametry

**Vstupní parametry** prvek obsahuje následující element:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| InputParameter | 1: n | Očekávaný vstupní parametr. |

#### <a name="inputparameter"></a>InputParameter

| Atribut | Požaduje se |Popis |
| --------- | ----------- |----------- |
| ID | Ano | Identifikátor, který je odkaz na parametr metody transformace deklarací identity. Každá metoda transformace deklarací identity má své vlastní hodnoty. V tabulce transformace deklarací identity pro úplný seznam dostupných hodnot. |
| DataType | Ano | Typ dat parametr, jako je řetězec, logická hodnota, Int nebo data a času podle výčtu datového typu ve schématu XML vlastní zásady. Tento typ se používá k provedení aritmetické operace správně. Každá transformace deklarací identity má své vlastní hodnoty. Zobrazit [odkaz na transformaci deklarací](#Claims-transformations-reference) úplný seznam dostupných hodnot. |
| Hodnota | Ano | Hodnota, která je předána verbatim transformace. Některé hodnoty libovolného, některé z nich můžete vybrat z metody transformace deklarací identity. |

### <a name="outputclaims"></a>OutputClaims

**OutputClaims** prvek obsahuje následující element:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| outputClaim | 0: n | Typ deklarace identity očekávaný výstup. |

#### <a name="outputclaim"></a>outputClaim 

**OutputClaim** prvek obsahuje následující atributy:

| Atribut |Požaduje se | Popis |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | Ano | Odkaz na typu deklarace identity, již definovaná v části ClaimsSchema v zásadách.
| TransformationClaimType | Ano | Typ deklarace identifikátoru tak, aby odkazovaly transformaci. Každá transformace deklarací identity má své vlastní hodnoty. Zobrazit [odkaz na transformaci deklarací](#Claims-transformations-reference) úplný seznam dostupných hodnot. |
 
Pokud vstupní a výstupní deklarací stejného typu (string a boolean), můžete použít stejný vstupní deklaraci identity jako výstup deklarace identity. V takovém případě transformace deklarací identity změní vstupní deklaraci identity s hodnotou výstup.

## <a name="example"></a>Příklad:

Například může ukládat poslední verzi služby, které uživatel přijal vaše podmínky. Při aktualizaci podmínky služby, můžete požádat uživatele tak, aby přijímal nové verze. V následujícím příkladu **HasTOSVersionChanged** transformaci deklarací identity porovnává hodnotu **TOSVersion** deklarace identity s hodnotou **LastTOSAcceptedVersion**deklarace identity a pak vrátí logickou hodnotu **TOSVersionChanged** deklarací identity.

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

## <a name="claims-transformations-reference"></a>Odkaz na transformaci deklarací identity

Příklady transformace deklarací naleznete na následujících stránkách odkaz:

- [Datový typ Boolean](boolean-transformations.md)
- [Datum](date-transformations.md)
- [celé číslo](integer-transformations.md)
- [JSON](json-transformations.md)
- [Obecné](general-transformations.md)
- [Účtu na sociální síti](social-transformations.md)
- [řetězec](string-transformations.md)
- [Třída stringCollection](stringcollection-transformations.md)

