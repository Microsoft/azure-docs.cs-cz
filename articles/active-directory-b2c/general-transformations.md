---
title: Obecná deklarace identity transformace příklady Identity prostředí Framework schéma z Azure Active Directory B2C | Dokumentace Microsoftu
description: Obecná deklarace identity transformace příklady Identity prostředí Framework schéma z Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 4e28dff6235e869c9275a8b0ba8d80252a9ea792
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55167372"
---
# <a name="general-claims-transformations"></a>Obecná deklarace identity transformace

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek obsahuje příklady použití obecné deklarace identity transformace schématu architekturu rozhraní identit v Azure Active Directory (Azure AD) B2C. Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="doesclaimexist"></a>DoesClaimExist

Ověří, zda **inputClaim** neexistuje nebo není a nastaví **outputClaim** na hodnotu true nebo false odpovídajícím způsobem.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |Všechny | Vstupní deklarací, jejichž existence je potřeba ověřit. |
| outputClaim | outputClaim | Boolean | Typ ClaimType, který je vytvořen po zavolání této ClaimsTransformation. |

Použijte Tato transformace na zkontrolujte, jestli deklarace identity existuje, nebo obsahuje všechny hodnoty deklarací identity. Vrácená hodnota je logická hodnota určující, zda existuje deklarace identity. Následující příklad zkontroluje, jestli existuje e-mailovou adresu.

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **inputClaim**: someone@contoso.com
- Výstupní deklarace identit: 
    - **outputClaim**: true

## <a name="hash"></a>Hodnota hash

Hash – zadaná jako prostý text pomocí sůl a tajný klíč.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | ve formátu prostého textu | řetězec | Vstupní deklaraci identity šifrování |
| InputClaim | Hodnota Salt | řetězec | Parametr řetězce salt. Můžete vytvořit náhodnou hodnotu pomocí `CreateRandomString` transformace deklarací identity. |
| InputParameter | randomizerSecret | řetězec | Odkazuje na existující Azure AD B2C **klíče zásad**. Chcete-li vytvořit nový: Ve vašem tenantovi Azure AD B2C vyberte **nastavení B2C > architekturu rozhraní identit**. Vyberte **klíče zásad** zobrazíte klíče, které jsou k dispozici ve vašem tenantovi. Vyberte **Přidat**. Pro **možnosti**vyberte **ruční**. Zadejte název (Zadaná předpona B2C_1A_ mohou být přidány automaticky.). V dialogovém okně tajného kódu zadejte jakékoli tajný klíč, který chcete použít, jako je například 1234567890. Použití klíče, vyberte **tajný klíč**. Vyberte **Vytvořit**. |
| outputClaim | Hodnota hash | řetězec | Zavolání typu deklarace identity, který je vytvořen po to transformace deklarací identity. Deklarace identity gurovaný `plaintext` inputClaim. |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **ve formátu prostého textu**: MyPass@word1
    - **řetězce Salt**: 487624568
    - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Výstupní deklarace identit: 
    - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=



